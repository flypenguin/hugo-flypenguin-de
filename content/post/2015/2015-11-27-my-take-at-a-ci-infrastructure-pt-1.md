---
title: My take at a CI infrastructure, Pt.1
author: penguin
type: post
date: 2015-11-27T19:09:55+00:00
url: /2015/11/27/my-take-at-a-ci-infrastructure-pt-1/
categories:
  - Longer things
tags:
  - aws
  - cicd
  - puppet

---
... so far.

It might be crappy, but I'll share it, cause it's working. (Well, today it started doing this 😉 ). But enough preamble, let's jump in.

## The Situation

I am in a new project. Those people have _nothing but a deadline_, and when I say nothing I mean it. Not even code. They asked me what I would do, and I said "go cloud, use everything you can from other people, so you don't have to do it, and you stay in tune with the rest of the universe" (read: avoid <a href="Not-invented-here-Syndrom" target="_blank">NIH syndrome</a>). They agreed, and hired me.

## The Starting Point

They really want the **JetBrains toolchain**, the devs use **CLion**. They also want **YouTrack** for ticketing (which doesn't blow my mind so far, but it's ok). Naturally they want to use **TeamCity**, which is the Jenkins alternative from JetBrains, and pretty all right from what I can see so far.

The code is probably 95%+ **C++**, and creates a **stateless REST endpoint** in the cloud (but load balanced). That's a really simple setup to start with, just perfect.

Source **code hosting** was initially planned to be either **inhouse** or in the bought cloud, not with a hoster. Up to now they were using git, but without graphical frontend which involved manual creation (by the - part time - admin) of every git repo.

## The Cloud Environment

_That's just practical stuff now, and has nothing - yet - to do with CI/CD. Skip it if you're just interested in that. Read it if you want to read my brain._

I looked around for full-stack hosted CI/CD systems, notably found only Shippable, and thought that they don't fully match the requirements (even when we move source code hosting out). So I went to AWS, and tried ElasticBeanstalk. This is quite cool, unfortunately scaling takes about 3-5 minutes for the new host to come up (tested with my little [load dummy tool][1] in a simple setup, which I actually didn't save, which was stupid).

Anyway, before deploying services CI (the compilation & build stuff) must work. So my first goal was to to get _something_ up and running ASAP, and thats bold and capitalized. Fully automated of course.

For any kubernetes/CoreOS/... layout I lack the experience to make it available _quickly_, and - really - all the AWS "click here to deploy" images of those tools didn't work out-of-the-box. So I started fairly conventional with a simple CloudFormation template spawning three hosts: TeamCity server, TeamCity agent, Docker registry, and - really important - GitLab. Since then GitLab was replaced by a paid GitHub account, all the better.

Setting the hosts up I used Puppet (oh wonder, being a Puppet "Expert"). Most of the time went in writing a [TeamCity puppet module][2]. A quirk is that the agents must download their ZIP distribution image from a running master only, which is kinda annoying to do right in puppet. For now TeamCity is also set up conventionally (without docker), which I might change soon, at least for the server. The postgres database [runs in a container][3], though, which is super-super simple to set up (please donate a bit if you use it, even 1€ / 1$ helps, that guy did a great job!). Same went for [gitlab][4] (same guy), and [redis][5] (again). I also used the anti-pattern of configuring the hosts based on their IP addresses.

I also wanted to automate host bootstrapping, so I [did this in the cloudformation template][6] for each host. The archive downloaded in this script [contains 3 more scripts][7] - a distribution-dependent one which is called first, have a look to see details. Basically it's just a way to download a snapshot of our current puppet setup (encrypted), and initialize it so puppet can take over. I also use "at" in those scripts to perform a reboot and an action after, which is highly convenient.

## CI (finally)

... in the next post 😉

 [1]: https://hub.docker.com/r/flypenguin/loadummy/
 [2]: https://forge.puppetlabs.com/flypenguin/teamcity
 [3]: https://hub.docker.com/r/sameersbn/postgresql/
 [4]: https://hub.docker.com/r/sameersbn/gitlab/
 [5]: https://hub.docker.com/r/sameersbn/redis/
 [6]: https://gist.github.com/flypenguin/c4df167ee155a7e4fa82
 [7]: https://gist.github.com/flypenguin/85dbddd7b15eaa515945