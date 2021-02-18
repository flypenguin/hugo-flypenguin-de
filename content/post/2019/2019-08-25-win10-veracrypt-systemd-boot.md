---
title: 'Win10 & Veracrypt & systemd-boot'
author: penguin
type: post
date: 2019-08-25T10:12:17+00:00
url: /2019/08/25/win10-veracrypt-systemd-boot/
categories:
  - Desktop shit
tags:
  - boot
  - linux
  - uefi
  - windows

---
There are some things seemingly _nobody_ does. For example, ...

  * double-booting Win10 and Linux
  * on an UEFI System
  * while the Win10 Partition is encrypted using VeraCrypt.

Yes, it's a complex scenario, but since MS in all of his (money-grabbing) wisdom does not include BitLocker in Win10 Home, this is a necessary precaution. I'll not go over the installation of both systems (pretty straightforward, and Arch Linux has - as always - a nice Wiki entry about it).

Unfortunately, **Win10 likes to break its own boot manager on updates,** which is very scary ("Your Windows partition is damaged"), and super annoying, but I think I got the solution now.

So, **the Linux-based (of course) solution for Windows 10 and VeraCrypt is:**

  * **First,** [use the Linux boot manager][1] (so add Win10 to Linux boot mgr and use this, not the other way around)
  * **Second,** in systemd-boot you have to [refer to the VeraCrypt boot EFI][2], _not_ the Win10 one:

```
# esp partition - /loader/entries/winvera.conf
title Windows 10 VeraCrypt
efi /EFI/VeraCrypt/DcsBoot.efi
```

This is in fact all you need to do. Now, if Windows fucks up its own boot loader, it seems systemd-boot just ignores everything, loads the correct VeraCrypt bootloader (as it is supposed to be), and all is well.

It _can_ happen though that Windows places its own boot manager back in front of systemd-boot again, so it's used as the default one. Then use [one of the methods described here][1], and you should be fine. (This did not happen to me, it always used the correct boot manager but fucked up Windows boot)

 [1]: https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface#Windows_changes_boot_order
 [2]: https://wiki.archlinux.org/index.php/systemd-boot#EFI_Shells_or_other_EFI_apps