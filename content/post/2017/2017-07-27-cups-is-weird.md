---
title: CUPS is … weird
author: penguin
type: post
date: 2017-07-27T20:11:24+00:00
url: /2017/07/27/cups-is-weird/
categories:
  - Uncategorized
tags:
  - arch
  - cups
  - printing

---
CUPS is the printing system developed by Apple which is now also in use on pretty much every other Linux / Unix / *BSD box there is. Unfortunately, the process of managing printers is way more painful than the process on Arch Linux. At least with my setup, which is i3 running in a weirdly crippled GNOME session.

This is what you have to do to get CUPS working on your machine:

<pre class="lang:default decode:true " title="Install CUPS on Arch linux">$ pacman -S cups
[...]
$ usermod -aG sys MY_USER
[...]
$ systemctl enable org.cups.cupsd
$ systemctl start org.cups.cupsd
</pre>

The magic is line 2 - adding your user to the sys group. If you don't do this, you won't be able to use the web interface at all. Which is a shame, since then you can't do anything useful (install printers, manage jobs, etc ...).