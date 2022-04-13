---
title: "First DevOps Live by Ondrej Sika: Managing GitLab with Terraform"
date: 2020-09-05T16:00:00.000Z
summary: About two hours of concentrated knowledge of Terraform and GitLab
  and GitHub. How to automate your work not just with your infrastructure, but
  also with GitLab. Manage your groups, projects, users and all of it together
  easily in a declarative way.
tags:
  - devops
  - devopslive
  - ondrejsika
  - livestream
  - terraform cloud
  - terraform
  - gitlab
---

**First DevOps Live livestream by Ondrej Sika, 2nd September 2020**

The topic was how to use Terraform to configure and managed your GitLab (including self-hosted instance) and GitHub.

Ondra began with simple question if everyone is familiar with Terraform and did actually quite big introduction to Terraform for people who never used it before. He also did show a custom tiny provider of his own (for Linode, built on top of official with some extra features).

After that, there was an introduction to Terraform Enterprise / Cloud, which is actually really powerful tool, way more than I though. I am looking forward for using it! Also the TF cloud can be managed via TF as well, which is great for teams and organizations. \
One more important thing to mention is, that all of the TF tasks are executed in Terraform Cloud and not locally, only output is being streamed back to your terminal. Output is also streamed to a web interface where you can see it live like in terminal.

And now to the GitLab! Ondra has presented how to manage organizations, users, projects and most importantly CI variables. No more clicking manually in GUI! Everything is declarative, versioned and executed in safe environment in CI and you can collaborate on that with all of advantages of GitLab. "Managing GitLab from Terraform in GitLab".

Since such a configuration started getting quite complex, we were also shown how to create Terraform modules, so you can easily reuse code without code duplication. With this wide introduction, it feels so easy to get started!

Almost everything can be managed via Terraform in GitLab, I would say that this was a life changer.

Unfortunately, since this was first livestream of DevOps Live, there was not much of time left for GitHub. Only a sneak peak was delivered, next time?

### Other articles

* [Ondrej Sika's blog post and recording of the livestream](https://ondrej-sika.cz/blog/zaznam-devops-live-1-sprava-gitlabu-pomoci-terraformu/)
* [Ondrej Sika's blog post on managing GitLab via Terraform](https://ondrej-sika.cz/blog/sprava-gitlabu-pomoci-terraformu/)

### Links

* [DevOps Live by Ondrej Sika](https://ondrej-sika.cz/devopslive)
* [Terraform](https://www.terraform.io/)
* [Terraform Registry](https://registry.terraform.io/)
* [Terraform provider for GitLab](https://www.terraform.io/docs/providers/gitlab/index.html)
* [Example tiny Terraform provider for Linode with some extra features](https://github.com/ondrejsika/terraform-provider-linodex)