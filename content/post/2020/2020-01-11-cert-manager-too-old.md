---
title: cert-manager too old …
author: penguin
type: post
date: 2020-01-11T00:28:39+00:00
url: /2020/01/11/cert-manager-too-old/
categories:
  - Uncategorized
tags:
  - helm
  - kubernetes
  - ops

---
Today cert-manager stopped issuing certificates, and all requests said "insecure website". Uncool, since this affected our Confluence and our sign-in mechanism. So let's find out what was happening, right? Turns out cert-manager considered itself "too old" ("your ACME client is too old", literally) and wanted to be updated.

So far, so good. Just perform <code class="EnlighterJSRAW" data-enlighter-language="shell">helm update cert-manager cert-manager</code>, right?

Wrong.

  * First, I had to [**upgrade to helm3**][1]. All right, I _could_ have used helm2, but helm3 was already on here, and it seemed easy. That went (fairly) easy.
  * Then I wanted to upgrade cert-manager. Turns out for that I actually had to **upgrade the running k8s version** 1.12.x to 1.13.x ... otherwise I'd get [errors from the helm chart][2]. That just took ages cause AKS is a bit slow.
  * Finally done I wanted to upgrade cert-manager. Until I realized a lot of **stateful pods were stuck in "initialization".** Turns out that AKS had issues moving the volumes around, I still don't know why. (Did I mention I just don't like pretty much anything about Azure? It's just _so_ incredibly cumbersome to use, and nothing is where you expect it). So I had to manually mount the volumes on the host the Pod was currently on and have an open TODO now, which just sucks.
  * Finally done I wanted to upgrade cert-manager. The upgrade went just peachy, until I realized that ... nothing happened. Turns out they changed pretty much all API versions and annotation keys. So I had to **rewrite / [upgrade all ingress][3] annotations, [update the ClusterIssuer resources][4] and delete the now obsolete former K8S CRDs.**

And just like that I had my certificates back. Wasn't that easy? 😀

&nbsp;

 [1]: https://helm.sh/blog/migrate-from-helm-v2-to-helm-v3/
 [2]: https://github.com/jetstack/cert-manager/issues/1143#issuecomment-471219707
 [3]: https://cert-manager.io/docs/installation/upgrading/upgrading-0.10-0.11/
 [4]: https://cert-manager.io/docs/installation/upgrading/upgrading-0.7-0.8/