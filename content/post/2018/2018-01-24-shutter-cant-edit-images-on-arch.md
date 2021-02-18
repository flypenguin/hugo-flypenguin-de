---
title: Shutter can’t edit images on Arch
author: penguin
type: post
date: 2018-01-24T11:55:36+00:00
url: /2018/01/24/shutter-cant-edit-images-on-arch/
categories:
  - General things
tags:
  - arch

---
Unfortunately [shutter][1] does no longer work (or not yet, maybe, hopefully 😉 with Wayland on Arch. But I still use it for image editing, namely screenshot annotations, for which this is the best tool by far I have ever found. Not to mention the most private one, cause everybody and his dog wants you to upload "to the cloud" nowadays.

On a freshly installed system you will find the "Edit" button grayed out though after you installed Shutter. Reason being there's a lib missing which is not installed by default. This is how you install it:

```default
$ yaourt -S perl-goo-canvas
```

And the editing continues.

(Original source: [this one][2]. Thanks!)

 [1]: https://aur.archlinux.org/packages/shutter/
 [2]: http://www.fosslinux.com/2168/how-to-enable-image-editor-in-shutter-in-manjaro-arch-linux.htm