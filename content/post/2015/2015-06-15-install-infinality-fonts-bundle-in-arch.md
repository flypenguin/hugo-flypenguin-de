---
title: Install infinality fonts bundle in arch
author: penguin
type: post
date: 2015-06-15T12:09:41+00:00
url: /2015/06/15/install-infinality-fonts-bundle-in-arch/
categories:
  - Uncategorized

---
Installing custom repos in Arch is kind of annoying. But here's how it goes (especially if you're behding a firewall which permits only ports 80, 443 and 22):

First. Switch the keyserver to a HTTP based one. To do this:

  1. edit /etc/pacman.d/gnupg/gnupg.conf
  2. replace "keyserver hkp://..." with "keyserver hkp://keyserver.kjsl.com:80"

Second. Get the key ID for the repo (in my case [from the wiki][1]):

  * KEY ID is "962DDE58"

Third. Download key from keyserver and sign it locally:

  * pacman-key -r 962DDE58
  * pacman-key -lsign-key 962DDE58

Fourth. Add the repository to the pacman.conf file:

  * "vim /etc/pacman.conf"
  * add the lines [from here][1]

Fifth. Update the repos

  * pacman -Sy

Sixth. Install the f\*** bundle:

  * pacman -S infinality-bundle

That's it 🙂

 [1]: https://wiki.archlinux.org/index.php/Unofficial_user_repositories#infinality-bundle