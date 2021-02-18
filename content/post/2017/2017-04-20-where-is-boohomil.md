---
title: Linux font rendering sucks, a.k.a “Where is Boohomil”?
author: penguin
type: post
date: 2017-04-20T14:58:05+00:00
url: /2017/04/20/where-is-boohomil/
categories:
  - General things
tags:
  - arch
  - desktop
  - fonts

---
For some reason, the maintainer behind the "*-infinality" packages in Arch Linux "has gone missing" for a while.

Why is that important to me? Because infinality is a patch set to a bunch of font and rendering packages, which make fonts under Linux look _**SO**_ much better than the default setup. (Yes, there are still a couple of things that Linux just _absolutely cannot compete in_ with Mac and / or Win, and font rendering is one of them. Ubuntu does a reasonable job of this, every other distro just sucks.

Except when you were using infinality. And now it's defunct.

Anyway, after [experiencing the unbelievably ugly phenomenon described in here][1], I tried this guide here now, and it seems to fix it.

  * [THE GUIDE (tm)][2]
  * [Alternate location of "THE GUIDE (tm)"][3]
  * [info source 1][4]
  * [info source 2][5]

 [1]: https://askubuntu.com/questions/125297/how-can-i-fix-ttf-fonts-ligatures-tt-ti-fi-ff-etc-in-firefox
 [2]: https://gist.github.com/cryzed/e002e7057435f02cc7894b9e748c5671
 [3]: https://www.ostechnix.com/fix-harfbuzz-infinality-issue-arch-linux/
 [4]: https://aur.archlinux.org/packages/freetype2-infinality/
 [5]: https://github.com/bohoomil/fontconfig-ultimate/issues/171