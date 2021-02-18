---
title: The state of things – technology
author: penguin
type: post
date: 2017-04-10T18:54:06+00:00
url: /2017/04/10/the-state-of-things-technology/
categories:
  - General things
  - Longer things
tags:
  - current state
  - management

---
It's been a while. I am currently pretty burned out, and the work keeps getting more. This is bad. But let's talk about some challenges right now. So this is an overview of our ...

#### Technical state

We're still using **Rancher**. Rancher is super cool, but has the annoying habit of _completely_ crashing about once every two months, leading to a full cluster outage for anything between 1-3 hours. Usually about 2. I still love it, but we matured in our needs, and maybe Rancher needs time to catch up (cause our needs are sometimes a bit "special"). But the Rancher team is making great progress in the right directions, and I am fully competent that Rancher will take a place in the orchestration space. Still we're thinking about moving to **K8s**, simply because _so much_ is already there.

We're using **Prometheus** for monitoring now. Rocks. Period.

We're still using **AWS**. Many of our customers would prefer Azure Germany. If you didn't know - Azure in Germany advertises a "Data Custodian" mode, or "Data Trustee" model, not sure how to translate this and too lazy to look it up. This means that in Germany the data centers are running the true Azure stuff, but they are actually fully operated by Deutsche Telekom.

Advantage, you ask? Easy. When the DOJ sends one of those super secure letters to Microsoft for "give me your data", they simply forward this to Deutsche Telekom. They will probably frame it on a wall somewhere, but I don't think they will actually give out the data. Problem solved. (We all hope :))

We are almost done with setting the whole cloud up using **Terraform**. It became a really mature project over the last year, and we are super happy with the progress it's making. Also, with Azure in the works for us (some cusomers ...) this is a cool way to just manage all with the same tooling. Infrastructure as code, eh.

We try to migrate away from Teamcity to **Jenkins**. We didn't succeed yet. Too little manpower.

But the more interesting thing is in the next post, for me at least 😉