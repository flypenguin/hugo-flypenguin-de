---
title: Rancher 2.4+ installation notes
author: penguin
type: post
date: 2020-05-17T17:57:53+00:00
url: /2020/05/17/rancher-24-installation-notes
categories:
  - Docker
tags:
  - rancher

---
I really like [Rancher][1]. Used in v1.x, now I'm back at a point where I might need it. Also I want to pitch it to my colleagues, which means I need to have a quick and dirty setup to fool around with. The Rancher Terraform (love it) modules are huge, and I wanted to have a simpler method (which I understand more) for my first training session with the guys.

These are my notes:

  * [The **Rancher Docker Container has its state under /var/lib/rancher**][2].
  * [For HA Rancher relies on a defined k8s service (probably just a DNS-resolvable service) called CATTLE\_PEER\_SERVICE][3]
      * [**HA installations** are currently **only supported in k8s clusters using helm** as installation method][4]
  * [The **Rancher** management server **can only be run on Kubernetes cluster** in an infrastructure provider where Kubernetes is **installed using K3s or RKE**][5]
  * **Installing Rancher in an Azure Container instance does _not_ work with persistent data**.
    Azure Files doesn't support symlinks, which Rancher needs. Of course that would have been my preferred way, because _super quick_ and _super slim_.
  * **Installing Rancher using the quickstart repo is a PITA on a Mac**.
    For two reasons: First you have to install the "rke" provider manually (really annoying), and second terraform can't import another module And basically a shit idea if you want to pitch this to your colleagures. You have to clone, install plugins, wrap modules, do ssh, all sorts of stuff which is just. Too. Complicated.

&nbsp;

 [1]: https://rancher.com
 [2]: https://rancher.com/docs/rancher/v2.x/en/installation/other-installation-methods/single-node-docker/advanced/
 [3]: https://github.com/rancher/rancher/issues/15851
 [4]: https://rancher.com/docs/rancher/v2.x/en/installation/how-ha-works/
 [5]: https://rancher.com/docs/rancher/v2.x/en/installation/k8s-install/