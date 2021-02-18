---
title: CI / CD solutions
author: penguin
type: post
date: 2015-12-07T10:43:44+00:00
url: /2015/12/07/ci-cd-solutions/
categories:
  - Uncategorized
tags:
  - cicd
  - cloud
  - services

---
Everyone wants free candy. Or a CI/CD solution, that ...

  * auto-deploys container-based servcies
  * auto-updates (roll-forward, roll-back) those services on keypress and "triggers"
  * has one-click-deployment of services.

_My_ definition of "service" here is "A set of containers working together in a certain way, automatically load balanced where needed". Example: A n worker nodes, loadbalanced from a web endpoint, and a database container. All deployed at the same time. Including one-click-deployment of environments ("Oh, I'd like to test _this_ revision again, let's deploy it quickly"...). Note that this is mostly CD (continuous deployment), cause CI is being done for a while now with - mostly - Jenins and other tools.

What I have found so far that seems to satisfy those requirements:

  * [Rancher][1] (note: [RancherOS][2] is _not_ the same, although from the same people)
  * [OpenShift][3]
  * (maybe) [Mesosphere][4]
  * [Kubernetes][5] (it seems, **updated 2015-12-10**)
  * [Vamp][6] (it seems, **updated 2015-12-10**)

And the service-only solutions without a tools tack which you can deploy locally:

  * [CoreOS' Tectonic][1]
  * [Shippable][7]
  * [Amazon ECS][8]

This is kinda it. I would love to evaluate  all those tools, but most of are not really AWS-deploy-friendly, and in the Shippable and Tectonic case they are paid full stack services without local (cloud-owned) deployment anyway. And most are in beta. But the scenery is becoming interesting ...

I will try to post my findings here, as well as the final choice I made for my current client, along with the reasons.

And for now: Mesosphere and Rancher looks really cool. And I mean "looks" - the UI is just pleasing (which is the most important selection criteria, I know 😉

**Update 2015-12-10:** Added Vamp, Kubernetes

 [1]: https://tectonic.com
 [2]: http://www.rancher.com/rancherio
 [3]: http://www.openshift.com
 [4]: https://mesosphere.com/
 [5]: http://kubernetes.io
 [6]: vamp.io
 [7]: http://shippable.com
 [8]: https://aws.amazon.com/de/documentation/ecs/