---
title: "Paved Paths Series - Part 3 - Why Paved Paths?"
subtitle: ""
date: 2023-05-06T10:43:59+01:00
lastmod: 2023-05-22T13:20:00+01:00
draft: false
description: "Part 3 of a series of posts exploring what paved paths mean in software engineering. This post explores why organisations should care about paved paths."
summary: "There is a tension that is constantly at play in software engineering teams - increase their autonomy to enable them to build without hand-offs, while reducing the same teams' cognitive load to allow them to focus on delivering value. With cloud computing, shifting left on everything, and maturing practices that enable continuous delivery, teams now have the potential to build, run and operate every aspect of their applications. But with this potential, comes the very real threat of burnout, skill gaps that lead to poor quality products, unwanted risks or slow time to market."
authors:
  - name: Richard Roché
    link: https://www.rickroche.com/
  - name: Chris Tacey-Green
    link: https://christaceygreen.com/

tags: ["paved paths", "platform engineering", "engineering enablement", "paved paths series"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "why-paved-paths-cover.png"
featuredImagePreview: ""
images: []

toc:
  enable: true
math:
  enable: false
lightgallery: false
---

{{< admonition type=info title="Paved Path Series" open=true >}}
This is Part 3 in a series of posts exploring Paved Paths between [Chris](https://christaceygreen.com/) and I.

- [Part 1 - Let's Talk About Paved Paths](https://www.rickroche.com/2023/04/paved-paths-series-part-1-lets-talk-about-paved-paths/)
- [Part 2 - Paved Paths: A One Pager](https://christaceygreen.com/blog/paved-paths-series-part-2-a-one-pager)
- [Part 4 - The Anatomy of Paved Paths](https://christaceygreen.com/blog/paved-paths-series-part-4-the-anatomy-of-paved-paths)
- Part 5 - The Spectrum of Platform Engineering and Paved Paths - Coming soon!
  {{< /admonition >}}

In this post we explore why organisations should care about paved paths.

## What problems do paved paths solve?

There is a tension that is constantly at play in software engineering teams - increase their autonomy to enable them to build without hand-offs, while reducing the same teams' cognitive load to allow them to focus on delivering value. With cloud computing, shifting left on everything, and maturing practices that enable continuous delivery, teams now have the potential to build, run and operate every aspect of their applications. But with this potential, comes the very real threat of burnout, skill gaps that lead to poor quality products, unwanted risks or slow time to market.

We've experienced both sides of this dichotomy - so let us quickly explore what each extreme looks like from our lived experience ...

### Centralised Capabilities

A centralised team (or many of them) abstract away areas of the tech stack from delivery teams. "Our engineers shouldn't need to know where their app is hosted". This reduces the cognitive load of the delivery team, which is great. Unfortunately, the delivery team now becomes hugely frustrated by a complete lack of autonomy, needing to log many tickets to the centralised team who have accidentally become a bottleneck. The delivery team also find that they struggle to make the centralised capabilities cover all of their requirements as these are constantly evolving. It's rigid, but they can't use anything else. Similarly, the centralised team becomes frustrated with an overwhelming number of tickets to complete and is not able to focus on creating self-service options.

### Decentralised Capabilities

Delivery teams own their entire stack. Amazing! Their quest for maximum autonomy has been achieved, and now they can finally get down to delivering features, without blockers from those pesky centralised teams. Except, they now wade through increasing cognitive load, solving the same problems in multiple places, often unaware that 3 other teams solved the problem 6 months ago. Similarly, the centralised team is now disempowered and watches on as more and more inconsistency spreads.

Clearly, neither extreme is a great place to be as an organisation. Fortunately, this is a problem that Paved Paths can solve well - balancing team autonomy without overwhelming teams with too much cognitive load while enabling self-service capabilities.

### Does this apply to all organisations?

Nope. Much like deciding when to build a monolith and when to split into microservices, building paved paths isn't going to help some sizes and shapes of organisations. If you're a startup with 5 engineers (and ChatGPT), you're unlikely to see the benefits of paved paths. Your alignment and quality assurance comes from sitting together 12 hours a day, working on the same repository, and having solid PR reviews.

Even as you grow, and have 3 separate engineering teams working on separate microservices, you can probably live without paved paths.

So how do you know you've reached the size or shape where paved paths are going to be a valuable investment? To answer this, you need to have a pulse on your tech stack and engineering practices. You'll start to see certain things popping up across your organisation. Sprawl of tooling. Inconsistent patterns. Engineer frustration. A slowing rate of change, or a higher rate of production failures. These are your signals. Your organisation has too many hikers fighting bears in the woods, and a paved path or two would really come in handy.

## Why invest in paved paths?

{{< admonition type=note title="Let's talk money" open=true >}}
Commercially, why do we care? Well, for a second let's ignore employee satisfaction and focus on our clients. We want to deliver great quality features to them, quickly. Our clients don't care about paved paths, they just want to see an incredible product with continuously improving and evolving feature sets. And with the rate that the technical landscape is changing right now, our products need to be able to iterate extremely quickly. To achieve this, our organisations should optimise for fast flow. We need to reduce the time it takes to go from "Hey, I've got an idea..." to "It's live!". This is what Paved Paths are designed for. They remove the weeks of debate, design (and possible disaster) from our delivery path and empower teams to experiment with and deploy features quickly.
**_Great ideas + Fast flow = $$$_**
{{< /admonition >}}

Paved Paths provide a codified home for all of your learnt best practices, for the things you want to standardise on, for ensuring that things like security, observability and other common components are baked into solutions and only need to be solved once. They also need to be constantly evolving and the usage of them becomes a necessary feedback loop.

In modernisation projects that we've worked on before, there have always been reference implementations built that aim to show teams how something could work. They do provide _some_ value. Teams can happily find them, copy-paste, and pick apart to suit their use case. The trouble with reference implementations is that they are generally only good for a specific point in time. If we were to breathe life into all of our documentation, reference implementations and written standards (with a bias towards enabling fast flow) we would start to get the essence of Paved Paths.

Paved paths are more than reference implementations, they are the building blocks in any internal developer platform - empowering teams to get up and running using well-understood design patterns with tried and tested technologies. They aim to provide a standardised experience that solves for common use cases, covering infrastructure, applications and operations.

{{< admonition type=note title="A brief rant about Standards" open=true >}}
For us, mentioning "standards" has the power to make us cringe so hard we bite our own teeth. They're not inherently bad things. They tend to come from a good place. The problem is that they tend to be entirely detached from the reality of an engineer's world. They live in a document somewhere, officially stamped with approval by experts and are then left to collect dust. Paved Paths are one of the few genuinely effective solutions to this problem, because they give your standards **life**. Your engineers are actively applying your standards because they're now present in their day-to-day.
Make this a rule: **if standards aren't part of your paved paths, they don't exist**.
{{< /admonition >}}

Invest in paved paths if you would like

- To make doing the "right thing" also the easiest thing, enabling clean architecture
- To increase team autonomy while reducing cognitive load
- To provide self-service, composable building blocks
- To solve common problems once that the 80% are going to experience

Additionally,

- They should be optional for teams to use, to enable experimentation where appropriate
- They are designed to provide the best user experience for engineers (DX)
- They should be secure by design
- They help you implement guard rails, while reducing gates

In a perfect world, teams would never have to learn the same lesson more than once in your organisation. Paved paths form the home where this shared consciousness is stored, iterated on and shared.

Up next, in part 4, we will unpack the anatomy of Paved Paths and all the components we believe should go into them - read it [here](https://christaceygreen.com/blog/paved-paths-series-part-4-the-anatomy-of-paved-paths).

Featured image background by [Megan Lee](https://unsplash.com/pt-br/@meganlee007?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/EsNA2bhBZ-8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
