---
title: "Paved Paths Series - Part 5 - The Spectrum of Platform Engineering and Paved Paths"
subtitle: ""
slug: "paved-paths-series-part-5-the-spectrum"
date: 2023-06-25T15:08:33+01:00
lastmod: 2023-06-25T15:08:33+01:00
draft: false
description: "Part 5 of a series of posts exploring what paved paths mean in software engineering. This post explores the extremes between a centralised model and a completely democratised model, and suggests a possible nirvana between the two."
summary: "In this post, we explore the extremes between a centralised model and a completely democratised model, and we suggest a possible nirvana between the two.

Platform Engineering is a hot topic right now - [QCon London]() had an entire track devoted to it in 2023, [PlatformCon 2023](https://platformcon.com/) saw a massive increase in attendees. It's seen some huge success in places like [Monzo](https://www.infoq.com/articles/cassandra-kubernetes-microservices/), where the shape of a microservice is strongly defined by a platform team, and engineers use a single CLI command to generate their instance on said platform. This model is great... when it suits your organisation."

tags: ["paved paths", "platform engineering", "engineering enablement", "paved paths series"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "the-spectrum-of-platform-engineering-and-paved-paths-cover.png"
featuredImagePreview: ""
images: []

toc:
  enable: true
math:
  enable: false
lightgallery: false
---

{{< admonition type=info title="Paved Path Series" open=true >}}
This is Part 5 in a series of posts exploring Paved Paths between [Chris](https://christaceygreen.com/) and I.

- [Part 1 - Let's Talk About Paved Paths](https://www.rickroche.com/2023/04/paved-paths-series-part-1-lets-talk-about-paved-paths/)
- [Part 2 - Paved Paths: A One Pager](https://christaceygreen.com/blog/paved-paths-series-part-2-a-one-pager)
- [Part 3 - Why Paved Paths?](/2023/05/paved-paths-series-part-3-why-paved-paths/)
- [Part 4 - The Anatomy of Paved Paths](https://christaceygreen.com/blog/paved-paths-series-part-4-the-anatomy-of-paved-paths)
- Part 6 - Measuring the Success of Paved Paths - Coming soon!
  {{< /admonition >}}

In this post, we explore the extremes between a centralised model and a completely democratised model, and we suggest a possible nirvana between the two.

Platform Engineering is a hot topic right now - [QCon London](https://qconlondon.com/) had an entire track devoted to it in 2023, [PlatformCon 2023](https://platformcon.com/) saw a massive increase in attendees. It's seen some huge success in places like [Monzo](https://www.infoq.com/articles/cassandra-kubernetes-microservices/), where the shape of a microservice is strongly defined by a platform team, and engineers use a single CLI command to generate their instance on said platform. This model is great... when it suits your organisation.

Paved Paths (or their similarly named counterparts) have been understandably embraced by the world of Platform Engineering. Using the [Team Topologies](https://teamtopologies.com/) platform topology, we know that the platform needs to provide a compelling internal product to accelerate delivery by Stream-aligned teams. For a platform to succeed, it needs some form of paved paths through it, which can be used to enable self-service by its users.

The problem with this, is that paved paths aren't **only** valid if you're following platform engineering principles. There's a range of approaches when creating paved paths that your organisation could follow, each of which have different benefits and drawbacks. Let's take a look at the spectrum...

## The Spectrum

{{< image class="image-center" src="the-spectrum.png" alt="The spectrum of platform engineering and paved paths" title="The spectrum of platform engineering and paved paths" width="90%" height="100%" linked="false">}}

### Platform Engineering

At one end of our spectrum, we have Platform Engineering. Engineering teams rely on clearly defined platforms to abstract away many of the 'things that aren't code'. In a lot of cases, this covers the infrastructure containing the application (Kubernetes, Networking, Storage, Monitoring, Resilience etc) as well as common patterns like observability, cost management and security. The engineers focus on the application code, pump out features, and don't bother themselves with things like ([Helm](https://helm.sh/)) or [Terraform](https://www.terraform.io/).

### You Build It, You Run It (YBIYRI)

At the other end of our spectrum, we have YBIYRI. Engineering teams own everything around their application. They write Terraform, or Bicep, or Helm Charts to define their infrastructure. They define the security configuration of their stack. Likewise, they own the resilience and scaling capabilities of their offering, and monitor all of it to ensure it's running just how they expect it to.

### Paved Paths

Sitting firmly in the middle of our extremes, are our paved paths. They're not just a subset of Platform Engineering. They actually provide an intriguing and powerful middle-ground for engineering teams, where we can balance some benefits and drawbacks of the two extremes. This _could_ suit your organisation better. Let's get into why...

## What do Paved Paths give us here?

YBIYRI has powerful implications on your people. Engineers will learn huge amounts about what it takes to serve digital products to clients. They'll own their entire Dev(SecFin...)Ops lifecycle, and start driving the strategy of your technology in ways they never could. This sounds a bit like a beautiful theory, but we've seen it in practice. Engineers who took this opportunity by the horns (and accepted the surrounding challenges), and thrived.
It's not all sunshine and rainbows though. Delivery can be slow. Cognitive load is high. The risk of burnout is always just over your shoulder, lurking among the many unknowns being juggled.

You may think Platform Engineering is the only escape from these issues. Abstract it all away. Your engineers only need to care about the code, and the platforms will take care of everything else. However, we need not jump to that extreme. Paved Paths offer us a great helping hand, without hiding innovation opportunities and growth from our engineers. Build paved paths, almost as if you had a platform underneath, but following a YBIYRI model. Engineering teams still own everything, but their cognitive load is reduced by commonly trodden, high-quality paths. Engineers can break away and innovate where they see value, but they still just use a wheel when they need a wheel.

## Risks

There are risks with this approach. Abstracting away complexity, without a platform team taking ownership of it, introduces a potential gap. At 2am, the engineering team is still expected to be able to debug an issue, they have no-one else to lean on. Mitigating this risk should mostly be a culture thing. A culture of continuous learning, where teams are encouraged and passionate about understanding their digital products, should fill this gap. Yes, engineers utilise paved paths to deliver faster, but they don't live in ignorance of what happens 'underneath'. Their architecture and tech stack is their own, paved paths simply accelerate their delivery. Obviously, the best culture in the world won't solve this alone, great documentation and a great developer experience is vital. But as long as you've read our [Part 4](https://christaceygreen.com/blog/paved-paths-series-part-4-the-anatomy-of-paved-paths), you should be in a solid position there!

## Ownership

By sitting ourselves in the centre of this spectrum, we raise a new question over ownership of the Paved Paths. As we've discussed, no specific team owns them. There isn't a platform team to rely on, and the engineering teams own their products. So how do we maintain them, and ensure they are still valuable to the community? Well, community engagement is a must. If your organisation is running quality production software, it already has the skills and subject-matter experts (SMEs) needed to maintain an incredible set of Paved Paths. They just need to be empowered and encouraged to own building blocks within them.

Engineering teams should contribute back to the Paved Paths they rely on, evolving them as they learn from production. They are the best informed on the state of the developer experience and are best placed to improve it. Encourage your teams to make time to contribute back.

Empower the SMEs in your organisation to contribute and own components of your paved paths. DBAs can pour their deep knowledge into sections relevant to databases, driving performance and cost-saving improvements across the organisation. Infrastructure experts can apply their expertise to areas like Infrastructure-as-Code, Networking, and Automation techniques. Security SMEs can take their standards and recommendations, and implement them within building blocks and pipeline tooling. For this to work, an [innersourcing](https://about.gitlab.com/topics/version-control/what-is-innersource/) model is a great idea - make this part of your paved path contribution model, and make the contribution guidelines clear.

Organisational structures don't need to change, if the right people are brought together and sponsored by leaders to contribute. As well as the organisational benefits, this is also incredibly attractive to SMEs. Rather than producing documents of standards, and then becoming a gate at some point in the delivery process, SMEs get to see their work 'live', being part of the solution rather than being a gate that is seen as slowing teams down.

There's one more group of people who can provide a huge amount of value towards Paved Paths when living on this spectrum. Enablement teams. We're not going to go into detail of what enablement teams are (maybe a future post outside this series!), but a key area of their contribution is towards these Paved Paths. By being well-connected to their engineering teams, enablement folks are incredibly well-placed to inform on the requirements of all building blocks, whilst also encouraging alignment and fast feedback loops. The team should not own them alone, but instead integrate the engineering community and SMEs with a clear strategy of the paths the organisation requires at that point in time.

{{< admonition type=note title="Is my organisation big enough for this?" open=true >}}
Absolutely! The size of an organisation will only affect the number of people you may need to scale out your engineering practices. Either side of the spectrum focuses effort in different ways, but doesn't dictate more or less people.

The most important factor here is sponsorship and focus. An organisation sponsoring long-term value, rather than short-term wins will succeed ([culture - part 4](https://christaceygreen.com/blog/paved-paths-series-part-4-the-anatomy-of-paved-paths#culture)).
{{< /admonition >}}

Up next, in part 6, we will discuss the important topic of measuring the success of your paved paths - coming soon!
