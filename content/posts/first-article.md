---
title: 'First article: JAMStack'
date: 2019-12-15T10:59:03.000Z
summary: "A few words about JAMStack and what I like about it."
tags: [jamstack, hugo, static website, netlify, cms]
---

It took so long, but I am finally here, with my blog! Hooray! 

Yes, yes. None really cares that I have a blog, but I do. For long I thought that [Hugo](https://gohugo.io) is something good, but UX wasn't the best. Today I have been proven wrong.

# JAMStack and Hugo
I knew about Hugo for a while now, but I wasn't into it much since at first look the templates looked complicated. But that is totally not the case! Go read the documentation, have a look at a theme and you are ready to get your site started.

I think I spent about 5 hours on this, mostly because I was always distracted by another good song from Spotify.


Only thing which is left for me to do now is to add a CMS. Yes, markdown and files are fine and will do just fine. But I am not always carrying laptop with me which has git installed. For that I decided to use [NetlifyCMS](https://www.netlifycms.org) since I am already hosting this site at [Netlify](https://netlify.com).

## Hugo
A little more about Hugo. It is written in [Go](https://golang.org) which is fine, but it excels at big sites with many articles and pages. It's performance is outstanding.

There are also other static site generators, few I know about (and very popular) are Jekyll and Gatsby. And many more, there is also API-first headless CMS, which is not JAMstack, but interesting aswell.

Nice thing about static generators is that you can write one yourself in almost any programming language. There are some in PHP for example. The benefit is that you can use language of your choice and get the blazing fast performance of static website especially if served via a CDN.

And since static generators and JAMStack are at rise, also many CMSs started appearing and they are great.

## VuePress
One thing I must mention is [VuePress](https://vuepress.vuejs.org), it is rising in popularity really fast and it is well deserved. It is static generator for documentation written in JavaScript nad Vue.js framework. It uses markdown files for content so most of the devs are already familiar with syntax and are ready to get started, it is so easy to get started and even with deploying, if you want go read some more yourself at it's docs. And if you are looking for example, checkout [Contributte](https://contributte.org) website - you can make much more then simple docs for your API!

# CMS for JAMStack
There is plenty of static CMSes out there, such as NetlifyCMS, [Fastly](https://www.fastly.com) which is awesome, but for my purpose I think it is overkill. And many more - I'm sure, but I feel like this will do.

## NetlifyCMS
In my opinion NetlifyCMS is awesome! It is simple to use and it has very simple UI aswell. It is based on git workflow. You can enable branch previews and many other awesome things I don't know about yet.

And to get you up and running, there are no setbacks. You do not need to write any backend for authentication, it is all handled by Netlify and their **Netlify Identity** service/functionality. 