---
title: "Creating This Website"
subtitle: ""
date: 2021-01-04T14:05:53+02:00
lastmod: 2021-01-04T20:31:53+02:00
draft: false
description: "How I setup this website and deployed to production"

tags: ["web", "hugo", "netlify"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "install-website.png"
featuredImagePreview: "install-website.png"
images: ["install-website.png"]

toc:
  enable: true
math:
  enable: false
lightgallery: false
---

I wanted to create a super simple website that is easy to maintain and easy to add content to. You are reading this on the finished product, here is what makes it tick.

<!--more-->

My searches started looking around for static website generators (I was hoping that I could run this without a database and all that jazz that normally gets dragged along) and stumbled upon [Hugo](https://gohugo.io/) whose [GitHub README](https://github.com/gohugoio/hugo) describes it as 

> A Fast and Flexible Static Site Generator built with love by [bep](https://github.com/bep), [spf13](http://spf13.com/) and [friends](https://github.com/gohugoio/hugo/graphs/contributors) in [Go](https://golang.org/).

This immediately ticked a few boxes for me being a Go fan, being able to keep everything in version control and write my posts in markdown. I dived right in by following the [Quick Start](https://gohugo.io/getting-started/quick-start/) and was up and running in a couple of minutes. Very awesome developer experience.

After reading [How to start a blog using Hugo](https://flaviocopes.com/start-blog-with-hugo/) I fiddled around with a few of the [themes](https://themes.gohugo.io/) available, initially choosing [anatole](https://themes.gohugo.io/anatole/) to get something up and running.

## The basics

With my initial investigations running on my local machine, the time for continuous deployment had arrived! First step: Get the code into version control... For me this means creating repo on [GitHub](https://github.com/) and pushing my initial code to [`main`](https://stevenmortimer.com/5-steps-to-change-github-default-branch-from-master-to-main/).

For the deployment of the website I decided to use [Netlify](https://www.netlify.com/). It's awesome and has a great developer experience - essentially add the site, linking your repo on GitHub (or other supported version control), Hugo gets picked up automagically and after hitting Deploy Site! you will be up and running in less than a minute. There is a [Step-by-Step Guide](https://www.netlify.com/blog/2016/09/29/a-step-by-step-guide-deploying-on-netlify/) to get you on your way if you get stuck.

I gave my site a nice name (`rick-roche`) to test using [rick-roche.netlify.app](https://rick-roche.netlify.app) immediately and pointed my domain to the Netlify name servers (DNS propagation time can take up to 72 hours) for this site to be live.

## Finishing up

There were a few features I was looking for such as search, extended markdown for adding copyable code snippets and the ability to add tips and notes like I am used to doing when using Confluence for work. I found the [LoveIt](https://themes.gohugo.io/loveit/) theme while browsing through the [Hugo Themes](https://themes.gohugo.io/) which had everything I was looking for. 

A few updates later (LoveIt has a lot of configurable options), code pushed to GitHub and an automatic deployment thanks to Netlify and you are looking at the results. 

Allowing Hugo and Netlify to do the heavy lifting I have ended up with a website that
- uses an open-source web framework (I like to be able to see the code)
- allows me to easily write content using markdown
- has continuous deployment for rapid publishing
- my entire website can live in version control

## References

I googled and read a bunch as I hacked my way through getting this site up. Here are links to the content that stood out!

- [Hugo](https://gohugo.io/)
- [Hugo Themes](https://themes.gohugo.io/) (I used the awesome [LoveIt](https://themes.gohugo.io/loveit/) theme
- [Start a blog with Hugo](https://flaviocopes.com/start-blog-with-hugo/)
- [Hugo best practices](https://github.com/spech66/hugo-best-practices)
- [A Step-by-Step Guide: Deploying on Netlify](https://www.netlify.com/blog/2016/09/29/a-step-by-step-guide-deploying-on-netlify/)

Featured image background by [NihoNorway graphy](https://unsplash.com/@nihongraphy?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText").
