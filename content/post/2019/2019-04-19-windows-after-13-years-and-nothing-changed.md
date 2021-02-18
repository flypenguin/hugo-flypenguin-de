---
title: Windows after 13 years – and nothing changed
author: penguin
type: post
date: 2019-04-19T09:48:35+00:00
url: /2019/04/19/windows-after-13-years-and-nothing-changed/
categories:
  - Uncategorized
tags:
  - annoying
  - windows

---
I have a Windows PC again, after about 13 years of abstinence and never looking back. (Why? Gaming. Once in 13 years is OK I guess).

And _nothing_ changed.

#### Step 1: Uninstalling crap

Uninstall those things from the Windows menu: Candy Crush, Cooking Fever, and three others I forgot to document. It's a pristine ISO install, nothing from a vendor - I bought components myself, and I assembled myself. So this is Windows and Windows alone that's to blame.

And don't forget all the crap which is in the Windows menu tiles - XBox & co, I mean you. (Removed about 7 super useless things here alone).

#### Step 2: change mouse wheel direction

Step 2: Change mouse wheel direction (sorry, Mac spoiled me). I can configure anything and everything in Windows - not that. Google helps, and I have to - of course - [navigate the registry][1] to find keys that look like this:

```
HKLM\SYSTEM\CurrentControlSet\Enum\HID\...
... VID_046D&PID_C53D&MI_01&COL01\9&12BDBF6B&0&0000\...
... DeviceParameters\FlipFlopWheel
```

(Set this to 1, and get the "VID_0..." whatever string from the "Advanced Settings" of the mouse properties dialogue. Brainfuck.

#### Step 3: Disable cortana

Oh yeah, disabling Cortana is almost easy (set this to 0):

```
HKLM\SOFTWARE\Policies\Microsoft\Windows\...
... Windows Search\AllowCortana
```

#### Step 4: Remove contacts icon from taskbar

Removing the stupid "Contacts" icon on the task bar is super simple in contrast: Right-click, and uncheck "Show contacts". Yay!

#### Step 5: Re-login / Reboot

Where the fuck can I log out?!

Oh right, click the start menu, immediately see the unobtrusive grey junk icon which is supposed to be me in the leftmost area on top of all the other nondescriminate icons, click it, and see the menu pop up which offers to "log out". How could I miss this.

#### Summary

Well, this is not all. This is just what I did _today_, after already tuning the system a while ago. In contrast Mac: Unpack, open (Laptops only here), start working. No candy crush removal necessary.

 [1]: https://www.windowscentral.com/how-reverse-scrolling-direction-windows-10