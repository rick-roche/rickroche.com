---
title: "Creating This ..."
subtitle: ""
date: 2020-12-30T14:05:53+02:00
lastmod: 2020-12-30T14:05:53+02:00
draft: true
description: "How I hacked this website together"

tags: ["web", "hugo", "netlify"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "turntable-1520671-639x427.jpg"
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""

---

... website? blog? I wanted to create a super simple website that is easy to maintain and easy to add content to. While I'm having fun figuring this out I'll try to remember to write some of it down!

<!--more-->

My searches started looking around for static website generators (I was hoping that I could run this without a database and all that jazz that normally gets dragged along) and stumbled upon [Hugo](https://gohugo.io/) whose [Github README](https://github.com/gohugoio/hugo) describes it as 

> A Fast and Flexible Static Site Generator built with love by [bep](https://github.com/bep), [spf13](http://spf13.com/) and [friends](https://github.com/gohugoio/hugo/graphs/contributors) in [Go][https://golang.org/].

This immediately ticked a few boxes for me being a Go fan, being able to keep everything in version control and write my posts in markdown. I dived right in by following the [Quick Start](https://gohugo.io/getting-started/quick-start/) and was up and running in a couple of minutes. Very awesome developer experience.

After reading [How to start a blog using Hugo](https://flaviocopes.com/start-blog-with-hugo/) I fiddled around with a few of the [themes](https://themes.gohugo.io/) available, initally choosing [anatole](https://themes.gohugo.io/anatole/) to get my site up and running.

## The basics

With my initial investigations running on my local machine, the time for continuous deployment had arrived! 

First step: Get the code into version control! For me this means creating repo on GitLab (I love GitLab) and pushing my initial code to `main`.

For the deployment of the website I decided to use [Netlify](https://www.netlify.com/). It's awesome and has a great developer experience - essentially add the site, linking your repo on Gitlab (or other supported version control), hugo gets picked up automagically and hit Deploy Site! There is a [Step-by-Step Guide](https://www.netlify.com/blog/2016/09/29/a-step-by-step-guide-deploying-on-netlify/) to get you on your way if you get stuck.

Updating my site name to be `rick-roche`; my website is available at https://rick-roche.netlify.app/.

Next, choosing a domain to host the site, I had previously bought `rickroche.com` making it an easy option

## Deployment
3. Deployment! I decided to use [Netlify](https://www.netlify.com/) to serve my website. It's awesome and has a great developer experience - add domain; add site from Gitlab; deploy!

## Adding more features

## Technology used

- Visual Studio Code
- Gitlab
- Hugo
- Netlify

## References

I googled and read a bunch as I hacked my way through getting this site up. Here are links to the content that stood out!

- https://gohugo.io/
- https://themes.gohugo.io/ (I used the awesome [anatole](https://themes.gohugo.io/anatole/) theme as a start and modified it as I went)
- https://flaviocopes.com/start-blog-with-hugo/
- https://github.com/spech66/hugo-best-practices
