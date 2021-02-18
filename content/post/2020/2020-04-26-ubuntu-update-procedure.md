---
title: Ubuntu update procedure
author: penguin
type: post
date: 2020-04-26T12:24:39+00:00
url: /2020/04/26/ubuntu-update-procedure/
categories:
  - Linux
  - Snippets
tags:
  - linux
  - ubuntu
  - updates

---
Brain dump.

```
# from here: https://askubuntu.com/a/862799/870970
apt-get update && apt-get --with-new-pkgs upgrade

# restart
reboot

# do it again
apt-get update && apt-get --with-new-pkgs upgrade

# remove shit
apt autoremove


#
# UPGRADE
#

do-release-upgrade


# done.
```

