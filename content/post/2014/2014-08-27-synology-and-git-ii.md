---
title: Synology and Git, II
author: penguin
type: post
date: 2014-08-27T17:58:42+00:00
url: /2014/08/27/synology-and-git-ii/
categories:
  - Uncategorized
tags:
  - git
  - nas
  - synology

---
Don't use the synology git package.

But then Git's not installed.

I found a compromise:

  * Install the package
  * disable it
  * switch back the shells in /etc/passwd from <something\_with\_git> to /bin/sh

Be happy.