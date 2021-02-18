---
title: Check MK container/k8s deployment
author: penguin
type: post
date: 2020-01-25T21:28:17+00:00
url: /2020/01/25/check-mk-and-containers/
categories:
  - Cloud
  - Infrastructure
  - Snippets
tags:
  - check_mk
  - fix
  - kubernetes
  - monitoring

---
In the company everybody seems to love [Check MK.][1] Me? Not so much, but a better alternative costs time and effort, both resources we don't really have right now. Yet there's a positive thing about it - because there's an [official docker container][2]. Since I already coded a helm chart for [stateful single container softwares][3] (which I personally find _super_ useful), I just wrote a Check MK YAML and installed it on my K8S cluster.

And then [nothing worked][4]. Turns out, Apache - which is used in that very strange "[Open Monitoring Distribution][5]" which Check MK seems to have been at one point - has a slightly sub-optimal configuration for running in a container behind a load balancer using cert-manager.

In short, you connect to the load balancer using "cmk.my.domain", and it redirects you to the container port, which to itself is "https://cmk.my.domain:5000/" and just wrong. Which brings me to the question if _anybody_ has _ever_ tried to run the Check MK container in a k8s cluster or behind a load balancer, which brings me to the question that I'd rather use software which actively embraces that, which brings me to the question _WHICH ONE?!?_ which brings us back to "no resources, no time".

So, bad luck, Check MK it is. But what about the bug? Reporting it you get an email "_DONT CALL US - WE CALL YOU (and we probably won't)_", with a ticket ID but no link. So probably no help here. So I "forked" the container, fooled around with it, and found a solution. The "fixed" container is now [available on docker hub][6] (sources on [GitHub][7]) and running nicely in our internal cluster. Let's see which hidden bugs I have introduced 😉 . The [stasico-Helm-YAML file I used to deploy Check MK][8] in K8S is also available.

##### TL;DR

  * Found a [bug in the Check MK docker container][4] - you can't run it behind a LB
  * [Created a fixed version][2] ([GitHub repo here][7])
  * ... and a [YAML deployment script][8] for my [stasico helm chart][3]

 [1]: https://checkmk.com
 [2]: https://hub.docker.com/r/checkmk/check-mk-raw
 [3]: https://github.com/flypenguin/helm-stasico/
 [4]: https://forum.checkmk.com/t/check-mk-running-behind-lb-on-port-80-redirects-to-url-including-port-5000/16545
 [5]: https://omdistro.org/
 [6]: https://hub.docker.com/repository/docker/flypenguin/check-mk-fixed/
 [7]: https://github.com/flypenguin/docker-check-mk-fixed
 [8]: https://github.com/flypenguin/helm-stasico/blob/master/examples/checkmk.yaml