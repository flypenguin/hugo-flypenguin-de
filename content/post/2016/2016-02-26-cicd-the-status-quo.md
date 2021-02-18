---
title: CI/CD, the status quo
author: penguin
type: post
date: 2016-02-26T16:49:01+00:00
url: /2016/02/26/cicd-the-status-quo/
categories:
  - CICD

---
... is quickly summed up: Working. 🙂

So I am - naturally - a happy camper. Although, there are a couple of things I would like to change, which don't scale or are in a state I'm not happy with for "v1.0" yet.

### Container data persistency

Rancher does offer [convoy][1], but I'm not sure it really fits my needs, and even if I'm not using it yet. And it starts to hurt not to have this. Badly. ([AWS Elastic File System][2] would be _excactly_ what I need, but that's not going to happen I fear). And even this is the most hurting part, not sure if this is the one I can solve quickest.

Pressure: 9

### Monitoring.

It's embarassing, but - **I don't have host monitoring**. I don't even remember how often I needed to re-create a host cause the disk went full without me noticing (and recreation is just so much easier than fixing, actually, so at least this is a good sign). The current evaluation candiate is [sysdig cloud][3]. That might change to [DataDog][4]. And don't get me started on log management.

Pressure: 8,5

### TeamCity.

While I like TeamCity, it's - like all things Java - horribly, completely, fully over-engineered mess with billions of functions which all don't _quite_ do what you want. In our case it's also reduced to executing basically 3 scripts. So TeamCity must go, mid term. Replacement? No idea. [Gradle][5], [drone.io][6], [wercker][7], [distelli][8] or [Travis][9] seem viable candidates. (Also found an [interesting article about online CI tools][10] which is well worth a read).

Pressure: 7

### Intmaniac.

It's written by me. And it sucks, and, although it works, it must go.

Pressure: 7

### Security.

Currently I am focused on "getting things to usable". Security is not on that list. I need to address some of the issues which I ignored until now, mainly because they might have architecture impact. (Which I believe I planned pretty well, but who knows until you have to actually do it, right?)

Pressure: 6

### Puppet.

I am still using Puppet. Might be overkill, but it's reliable once set up. That brings me to ... the set up. I am still using masterless puppet, one environment, and pulling the repo from each host each time. Simple, robust, working, but not elegant. I see different environments coming up, and then ... hm. It's gonna be complicated.

Pressure: 6

### Rancher.

[Rancher][11] is awesome. But I'm so desperately waiting for [this feature right here][12] to arrive it's not funny any more 🙂 . I also need the same thing for host-based services (which are not inside of a container orchestration platform). I want to spawn a service somewhere, and an Amazon Route53 entry should appear automatically. Preferably based on a consul readout. So DNS management becomes a non-issue.

Pressure: 5

### HA.

If one host breaks, the whole cloud is inoperable. Which one? The NAT host. Needed for any host to get a connection to the outside in AWS. And that's just one breaking point of a couple. Critical? Not really. Super annoying? You bet.

Pressure: 4

### Cloudformation.

Currently I use cloudformation to set everything up. And although it's an awesome product, it's kinda limited. If a host is gone, I can't call "cloudformation fix-it" and _this very host_ will respawn. With tools like terraform this is possible, but terraform relies (or relied the last time I looked) on local state data, which is a big pffft. It also means to really test an updated cloudformation template I have to recreate the full thing, which is just too cumbersome. (Takes about 1 hour to completely migrate everything, if done quickly, which is awesome for a complete outage, but really bad for testing). But maybe I just don't know enough and my template is way too tightly written.

Pressure: 3

&nbsp;

 [1]: http://docs.rancher.com/rancher/rancher-services/storage-service/
 [2]: https://aws.amazon.com/de/efs/
 [3]: http://www.sysdig.com
 [4]: http://www.datadoghq.com
 [5]: http://www.gradle.org
 [6]: http://www.drone.io
 [7]: http://www.wercker.com
 [8]: http://www.distelli.com
 [9]: http://www.travis-ci-com
 [10]: http://lukasmartinelli.ch/cloud/2015/04/04/github-ci-services.html
 [11]: http://www.rancher.com
 [12]: https://github.com/rancher/rancher/issues/2288