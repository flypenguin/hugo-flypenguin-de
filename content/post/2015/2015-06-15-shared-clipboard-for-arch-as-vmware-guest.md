---
title: Shared clipboard for Arch as VMWare guest
author: penguin
type: post
date: 2015-06-15T19:33:01+00:00
url: /2015/06/15/shared-clipboard-for-arch-as-vmware-guest/
categories:
  - Uncategorized
tags:
  - arch
  - vmware

---
... aaaand I wanted to have a shared clipboard. It's again all in the wiki, but again a bit distributed. So here we go.

First: Install open-vm-tools and gtkmm, then add some modules to system bootup

  * "sudo pacman -S open-vm-tools [gtkmm][1]"
  * "sudo vim /etc/mkinitcpio.conf"
  * Under "MODULES=..." add the following: "vmxnet3 vmw\_vmci vmw\_pvscsi vmw_balloon"
    (You probably don't need most of them, but this is the config which worked for me. I didn't try to remove them one-by-one to see which ones are _actually_ needed)
  * "sudo mkinitcpio -p linux"
  * reboot

Second, make sure "vmware-user-suid-wrapper" is stared on login:

  * echo vmware-user-suid-wrapper > ~/.xinitrc
  * Logout and log in (or reboot)

Done.

 [1]: https://bbs.archlinux.org/viewtopic.php?pid=1116899#p1116899