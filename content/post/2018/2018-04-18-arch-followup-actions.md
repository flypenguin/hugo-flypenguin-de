---
title: Arch followup actions
author: penguin
type: post
date: 2018-04-18T08:26:09+00:00
url: /2018/04/18/arch-followup-actions/
categories:
  - Linux
tags:
  - arch

---
Once you've installed Arch Linux, a couple of things are ... nice.

### Packages

```default
# standard
ack
cups
dnsmasq
git
gnome-extra
NetworkManager
network-manager-applet
ripgrep
vim
zsh

# yaourt
firefox-beta-bin
ttf-ms-win10
visual-studio-code-bin
```

&nbsp;

### Configurations

For network manager, [I prefer dnsmasq as the tool of choice][1], especially when using VPN connections:

```default
[Main]
dns=dnsmasq
```

### Enable services

```default
# enable
systemctl enable NetworkManager
systemctl enable org.cups.cupsd

# start
systemctl start NetworkManager
systemctl start org.cups.cupsd
```

### To-be-updated

... from time to time 😉

 [1]: https://wiki.archlinux.org/index.php/Dnsmasq#NetworkManager