---
title: FritzBox, Unify Security Gateway, and router replacements
author: penguin
type: post
date: 2020-04-19T20:39:47+00:00
url: /2020/04/19/fritzbox-unify-security-gateway-and-router-replacements/
categories:
  - Infrastructure
tags:
  - avm
  - fritzbox
  - home network
  - unifi
  - usg

---
My respect for network changes went up two or three ... hundred notches.

What happened. At my mother's place, there was a German FritzBox, which handles a VPN between my place and hers, phone lines, the doorbell, WiFi, DHCP, and maybe even more. Now we rent our parts of our house with AirBnB, and those people want - WiFi.

I don't want them to be in the same WiFi as myself and - more important - my mother, also I wanted to manage the whole house-WiFi from one central place. Unify / Ubiquity does a _great_ job of delivering really capable products to a reasonable price, so I went for it. During the whole weekend (really, every waking second except for two 10k runs to get the anger out of my system) this is what I learned:

  * [This article][1] about how to configure a FritzBox to _just_ do the modem part is still and fully correct for 1und1 in Germany. 
      * FritzBox PPPoE passthrough might not work if the model is too old. It won't tell you anything.
      * You don't need to set a VLAN ID in the USG then.
      * You do not need to prefix the 1und1 username with "H", at least for my contract.
  * You _must not_ configure any duplicate IPs in the Unify Control Center, _ever_. The thing works declaratively - if you hit "apply" or "save" the Control Center tries to _create_ this scenario. 
      * What do I mean? I configured the local "network" in the USG with the existing router/gateway IP. But when hitting "save" the USG is immediately configured to use that IP, battling the FritzBox, and provisioning _will_ fail.
  * A FritzBox cannot connect two site networks without the usage of the proprietary "MyFritz" service out of the box. You have to tinker. Which sucks. (Really, AVM?! _No_ site2site VPNs without MyFritz?!) 
      * This is the last open item btw, this is the only thing I couldn't rebuild so far.
  * "systemd-resolved" really want to use DNSSEC. It shouldn't when using a USG as name server though.
  * <code class="EnlighterJSRAW" data-enlighter-language="generic">/etc/systemd/resolved.conf</code> does not like comments after settings ("<code class="EnlighterJSRAW" data-enlighter-language="generic">THING=value # with a comment behind</code>" will _not_ work)
  * cron jobs are "out", using systemd timers is "in".
  * a systemd timer has to be treated the same way as a systemd service ("<code class="EnlighterJSRAW" data-enlighter-language="generic">systemctl enable thing.timer ; systemd start thing.timer</code>")
  * systemd _really_ wants to do everything 
      * ... but I kinda like it, it's one thinking behind everything, and really flexible
      * ... if it works.
      * ... which it sometimes really doesn't
      * ... and then you have no f*cking clue why.

In the end, I'm about 90% there. The results are very, very good - Unifi does a great job in providing ready-for-use products which satisfy SMB requirements just fine. Some annoyances, well, really solid and flexible and _insanely useful_ nonetheless.

 [1]: https://deer-it.de/fritzbox-7412-modem-bridge-mode-pppoe-passthrough/