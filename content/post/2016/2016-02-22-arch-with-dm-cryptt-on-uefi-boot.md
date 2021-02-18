---
title: Arch with dm-cryptt on UEFI boot
author: penguin
type: post
date: 2016-02-22T17:49:13+00:00
url: /2016/02/22/arch-with-dm-cryptt-on-uefi-boot/
categories:
  - Snippets

---
Just a collection of links, cause this is something which is not really documented in full. I am also too lazy, but next time I don't want to search 🙂 .

  * Basic installation instructions: [3rd party article][1] (German), [Arch Linux docs][2] (without LUKS)
      * _Read the comments_ on the first article: gummiboot is now [bootctl][3], you have to format the boot partition with FAT32, and you should use a better random generator on cryptsetup. It's all in there.
      * **Note:** The former /boot partition **is identical to** the UEFI boot partition. You don't need both.
  * Arch Linux original instructions: [In German][4], [in English][5]
      * **Careful:** The setting "FONT_MAP" in /etc/vconsole.conf in the _German_ guide should **not** be applied! It's obsolete.
      * The English guide does only go into the crypt installation, but that really deep.
  * If you actually added FONT_MAP to /etc/vconsole.conf, [that happens][6]
  * The file /vmlinuz-linux [comes in the "kernel" package][7]. If it's missing, just reinstall "pacman -S kernel"
  * You can usually choose between NetworkManager and systemd-networkd as networking management solutions. I chose NetworkManager:
      * systemctl enable NetworkManager
      * systemctl start NetworkManager
      * systemctl disable systemd-networkd
      * systemctl disable systemd-resolved
  * [Installing GNOME][8] (and the login manager gdm) is pretty simple:
      * pacman -S gnome gnome-extras
      * systemctl enable gdm

That should be it.

 [1]: https://thomas-leister.de/open-source/linux/arch-linux-luks-verschluesselt-auf-uefi-system-installieren-2/
 [2]: https://wiki.archlinux.de/title/UEFI_Installation
 [3]: https://wiki.archlinux.org/index.php/Systemd-boot#Configuration
 [4]: https://wiki.archlinux.de/title/Systemverschl%C3%BCsselung_mit_dm-crypt
 [5]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system
 [6]: https://bbs.archlinux.org/viewtopic.php?id=185189
 [7]: https://bbs.archlinux.org/viewtopic.php?id=156542
 [8]: https://wiki.archlinux.org/index.php/GNOME