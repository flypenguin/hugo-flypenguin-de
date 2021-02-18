---
title: Install Arch with full disk encryption, btrfs and EFI
author: penguin
type: post
date: 2018-01-20T14:13:38+00:00
url: /2018/01/20/arch-full-disk-encryption-btrfs-on-efi-systems/
categories:
  - General things
tags:
  - arch
  - linux

---
I recently had to re-install my beloved Arch Linux. For security I need (and use) full disk encryption. This is a cheatsheet for the whole procedure, because although the Arch Linux Wiki is _excellent_, it is also _huge_ and sometimes you must pick your stuff together from many pages.

This is what I am doing here 🙂

**NOTE:** Usually you only have to follow the one subsection I link to!

## Overview

One after another, we will do the following steps

  * Download and prepare Arch USB stick (skipped, you should know that 😉
  * Prepare the hard disk
  * Prepare the disk partitions
  * Add LVM "inside" the crypted partition
  * Create filesystems & mount partitions
  * Install arch
  * Configure boot manager

## Prepare the hard disk

Use `parted` to init the disk and ...

  1. [init the disk using a GPT partitioning scheme][1], then create
  2. [a GPT boot partition and put 100% of the remaining space in another partition][2] (the first two actions behind the link)

## Prepare the disk partitions

Basically,

  1. [use the `cryptsetup` command to encrypt the main (big) partition][3],
  2. [and create a file system on the boot partition][4] (remember: it _must_ be FAT32 for EFI boot, and it _must_ be _un_encrypted!)

## Add an LVM "inside" the encrypted partition

Cause we want "properly" encrypted swap (you can also encrypt swap using a `/dev/random` key every time, but then you will not persist data between reboots and you can't do things like suspend-to-disk), we need at least two "partitions" "inside" the crypted volume. Sounds like [LVM on LUKS][5]? It does. We already used it 🙂 .

  1. [Create LVM partitions inside the encrypted volume][6] (Don't forget to use `cryptsetup luksOpen` before, usually in step 1 in the last section 🙂

**NOTE:** Do **_not_** follow the above link down to "prepare the boot partition", cause they use ext2 and we need FAT32 for EFI boot partitions. Just don't.

I use the name "secure" for the VG, and I use btrfs cause I am so incredibly elite, and so we don't need to set a specific size for the `/` and `/home` "partitions" and can just use btrfs subvolumes, while _still_ being able to wipe the system without the home directories. That's pretty neat if you need it (I never did, but now I can ;). So that's the final setup:

```default
/dev/mapper/secure-swap    40 GB, swap
/dev/mapper/secure-system  rest, btrfs with 2 subvols: root & home
```

## Create filesystems & mount partitions

Of course, Arch has already a [wiki page section][7] for that. I did it 3 times in a different way until I found it and had to do it again. So here is my summary.

```default
# CREATE BTRFS & SUBVOLUMES
$ mkfs.btrfs /dev/mapper/secure-system
$ mount /dev/mapper/secure-system /mnt
$ btrfs subvolume create /mnt/@
$ btrfs subvolume create /mnt/@home
$ btrfs subvolume create /mnt/@snapshots
$ umount /mnt

# MOUNT PARTITIONS
$ mount -o subvol=@ /dev/mapper/crypted-system /mnt
$ mkdir -p /mnt/home /mnt/boot
$ mount -o subvol=@home /dev/mapper/crypted-system /mnt/home
$ mount /dev/sda1 /mnt/boot
```

**NOTE:** `/boot` is _not_ on an encrypted partition 😉 , and the leading "`@`" is a convention for subvolumes which should be mounted somewhere. I also don't use `compress=...`  parameters, cause I don't need / want transparent compression.

## Install arch

Then you follow up with the [usual installation procedure][8], but you **stop at** the "Initramfs" section. Here we will pick up again.

## Configure boot manager

We are using systemd-boot. Or `bootctl`, as the binary is called. It should be already installed. The procedure is [also outlined here][9]. We also enable TRIM support, it seems to lessen security, but it raises SSD performance and life time.

  1. First, check if your system [EFI is all right][10].
  2. **Optionally** [install the Intel microcode updater][11] package if you have an Intel CPU by doing `pacman -S intel-ucode`.
  3. Then run ... `bootctl -path=/boot install` to install systemd-boot.

Now create those files (all inside `/mnt` and relative to it, but of course you should be in a chroot right now :):

```default
title Arch Linux
linux /vmlinuz-linux
initrd /intel-ucode.img       # ONLY FOR INTEL CPUs!!
initrd /initramfs-linux.img
options luks.uuid=FS_UUID root=/dev/mapper/secure-system rootflags=subvol=@ rd.luks.options=discard
```

You can get FS_UUID in the options line above by using the `blkid` command. If you don't want to copy the UUID by hand, you can start console mouse support with copy-on-mark and paste-on-middleclick with `gpm -m /dev/input/mice -t imps2`. **Note** that the FS_UUID is the UUID of the _encrypted luks partition_, and **not** the filesystem within!

The list of [normal][12] and [dm-crypt related][13] kernel parameters ... well, is also in the Arch wiki.

```default
default arch    # the file above without .conf extension, can have wildcards!!
timeout 2
editor  0
```

```default
# Just MODIFY that file, to be precisely this line:
HOOKS=(base systemd autodetect modconf keyboard sd-vconsole block sd-encrypt sd-lvm2 filesystems fsck)
```

The key idea is to use the ["systemd" parameters instead][14] of the "normal" ones. The [full list of hooks][15] is of course also available, and the order is important.

Now execute:

```default
mkinitcpio -p linux
```

... and actually, that should be it.

```default
$ reboot
```

### Edits:

  * 2018-03-27 fixed a typo in the HOOKS documentation, clarified kernel boot parameters

 [1]: https://wiki.archlinux.org/index.php/GNU_Parted#Create_new_partition_table
 [2]: https://wiki.archlinux.org/index.php/GNU_Parted#UEFI.2FGPT_examples
 [3]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#Preparing_the_disk_2
 [4]: https://wiki.archlinux.org/index.php/EFI_System_Partition#Format_the_partition
 [5]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS
 [6]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#Preparing_the_logical_volumes
 [7]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#Creating_btrfs_subvolumes
 [8]: https://wiki.archlinux.org/index.php/Installation_guide#Installation
 [9]: https://wiki.archlinux.org/index.php/Systemd-boot
 [10]: https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface#Requirements_for_UEFI_variable_support
 [11]: https://wiki.archlinux.org/index.php/Microcode#Installation
 [12]: https://wiki.archlinux.org/index.php/kernel_parameters#Parameter_list
 [13]: https://wiki.archlinux.org/index.php/dm-crypt/System_configuration#Using_sd-encrypt_hook
 [14]: https://wiki.archlinux.org/index.php/Dm-crypt/System_configuration#mkinitcpio
 [15]: https://wiki.archlinux.org/index.php/mkinitcpio#HOOKS