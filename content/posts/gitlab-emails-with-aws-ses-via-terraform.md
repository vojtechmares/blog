---
title: GitLab emails with AWS SES via Terraform
date: 2022-04-12
summary: GitLab SMTP configuration for AWS SES with domain verification and DNS records managed by Terraform
tags:
  - devops
  - gitlab
  - terraform
  - ses
  - aws
---

> **NOTE**: I've setup the SES for my GitLab two years ago, but just now I finally get into writing the blog post.

# Motivation

In GitLab's documentation, it is recommended to install [Postfix](https://www.postfix.org/) for outgoing mail like password resets, notifications about failed CI pipelines and granted permissions for groups and projects.

From my experience, it takes a really long time to deliver the email if it is even delivered at all. Which for notifications, I do not mind, but for password reset is completely useless.

And this behavior is totally unacceptable for production. On my personal GitLab instance, it is fine, but still...

Because of that, I have explored other options of sending emails. I knew I wanted to go for managed solution with reasonable price, I am paying 15€ per month for a virtual machine running the GitLab instance (no CI runner). I did not want to pay 10€ per month for emails.

Mailgun would be a nice option, but their free tier is only a trial which is free for first three months before moving to paid plan.

As far as I know, you can't use Google's SMTP servers to send mail, so that is out of the window too.

I decided not to look any further, since AWS's pay-as-you-go pricing model fits my case nicely, since I will send a couple dozen emails per month tops, I was not worried about the bill at all.

# AWS SES it is, how do I configure it?

I went directly to AWS docs for guidance on how to setup SES. At the time I was learning Terraform so I thought that it would be really nice thing to write in Terraform to teach myself a little more.

To get domain ready for sending out emails via SES, you need to do a couple things first:
- validate the domain (proof of ownership) via DNS
- setup DNS DKIM records
- setup DNS SPF (TXT) records

And it is also recommended to setup DNS DMARC (TXT) record.

## Terraform is easy

I will start by sharing the Terraform code first and explaining it later. Altho I think it is pretty straight forward.

```terraform
##
# Cloudflare
##

data "cloudflare_zone" "zone" {
  name = "example.com"
}

# DNS
resource "cloudflare_record" "gitlab" {
  zone_id = data.cloudflare_zone.zone.zone_id
  name    = "gitlab"
  value   = "__REDACTED__"
  type    = "A"
  proxied = true
}

resource "cloudflare_record" "ses_verification_gitlab" {
  zone_id = data.cloudflare_zone.zone.zone_id
  name    = "_amazonses.${aws_ses_domain_identity.gitlab.id}"
  type    = "TXT"
  value   = aws_ses_domain_identity.gitlab.verification_token
}

resource "cloudflare_record" "txt_dkim_gitlab" {
  zone_id = data.cloudflare_zone.zone.zone_id
  count   = 3
  name = format(
    "%s._domainkey.%s",
    element(aws_ses_domain_dkim.gitlab.dkim_tokens, count.index),
    cloudflare_record.gitlab.hostname,
  )
  type  = "CNAME"
  value = "${element(aws_ses_domain_dkim.gitlab.dkim_tokens, count.index)}.dkim.amazonses.com"
}

resource "cloudflare_record" "txt_spf_gitlab" {
  zone_id = data.cloudflare_zone.zone.zone_id
  count   = 1
  name    = "gitlab"
  type    = "TXT"
  value   = "v=spf1 include:amazonses.com -all"
}

##
# AWS
##

# IAM user & policy for sending emails
resource "aws_iam_user" "ses_gitlab_user" {
  name = "gitlab-emails"
}

data "aws_iam_policy_document" "ses_gitlab_user" {
  statement {
    effect    = "Allow"
    actions   = ["ses:SendRawEmail"]
    resources = ["*"]
  }
}

resource "aws_iam_user_policy" "ses_gitlab_user_policy" {
  user   = aws_iam_user.ses_gitlab_user.name
  policy = data.aws_iam_policy_document.ses_gitlab_user.json
}

resource "aws_iam_access_key" "ses_gitlab_user" {
  user = aws_iam_user.ses_gitlab_user.name
}

output "gitlab_ses_access_key" {
  value       = aws_iam_access_key.ses_gitlab_user.id
  description = "Access key for SES for GitLab"
  sensitive   = true
}

output "gitlab_ses_smtp_password_v4" {
  value       = aws_iam_access_key.ses_gitlab_user.ses_smtp_password_v4
  description = "SES SMTP password for SES for GitLab"
  sensitive   = true
}

# Domain identity & verification
resource "aws_ses_domain_identity" "gitlab" {
  domain = cloudflare_record.gitlab.hostname
}

resource "aws_ses_domain_identity_verification" "gitlab" {
  domain     = aws_ses_domain_identity.gitlab.id
  depends_on = [cloudflare_record.ses_verification_gitlab]
}

# DKIM
resource "aws_ses_domain_dkim" "gitlab" {
  domain = cloudflare_record.gitlab.hostname
}
```

As I have mentioned before, there is not much to it. A few DNS records... and a domain verification setup.

At `aws_ses_domain_identity_verification.gitlab` resource, there is Terraform's `depends_on` clause to tell Terraform to create a DNS record first and then to try create a verification resource.

Also the `cloudflare_record.txt_dkim_gitlab` resource uses count to create three DKIM records, pseudo-looping over `aws_ses_domain_dkim.gitlab.dkim_tokens`.

As a part of the Terraform declaration, there is an IAM user with it's access key and SES SMTP Password v4 outputs. These are the credentials of the SMTP server to authenticate with.

## AWS SES sandbox

In order to send emails to other domain(s) then the domain you are sending from, you need to disable SES sandbox mode which so far can be disabled only by AWS support.

So I created a ticket, explained my situation that this is for my GitLab (and possibly in the future for other services). I asked for generous 5000 outgoing emails a month and hoped for the best.

It was accepted on the first try, no questions asked. Finally, the emails were free and I could move on with sending my emails!

## GitLab configuration

For the GitLab Omnibus instance, the config is stored at `/etc/gitlab/gitlab.rb` and you need to change your configuration to this.

```ruby
# Emails (SMTP)
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "email-smtp.<your target AWS region>.amazonaws.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "<AWS Access Key ID>"
gitlab_rails['smtp_password'] = "<AWS SES SMTP Password v4>"
gitlab_rails['smtp_domain'] = "<SMTP domain, e.g. gitlab.example.com>"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['gitlab_email_from'] = '<email address to send mail from, e.g. no-reply@gitlab.example.com>'
```

If you are using port `587` like I do, do not forget to enable `STARTTLS`!

# Summary

In summary, I believe this configuration is pretty straight-forward with no hick ups. It took me about 15 minutes to configure and I did not need to touch the configuration ever since.
