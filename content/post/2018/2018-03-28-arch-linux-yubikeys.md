---
title: Arch linux + yubikeys
author: penguin
type: post
date: 2018-03-28T13:18:04+00:00
url: /2018/03/28/arch-linux-yubikeys/
categories:
  - Linux
tags:
  - arch
  - yubikey

---
To use "ykman" for arch linux, you do this:

<pre class="lang:default decode:true">$ yaourt -S yubikey-manager pcsclite                 # THESE PACKAGES
[...]
$ systemctl start pcscd                              # START SERVICE
$ ykman info                                         # TEST
Device type: YubiKey NEO
Serial number: 0123456
Firmware version: 3.4.3
Enabled connection(s): OTP+U2F+CCID

Device capabilities:
    OTP:	Enabled
    U2F:	Enabled
    CCID:	Enabled
    OPGP:	Enabled
    PIV:	Enabled
    OATH:	Enabled
$ _</pre>

Sounds easy? Still had to google the things.