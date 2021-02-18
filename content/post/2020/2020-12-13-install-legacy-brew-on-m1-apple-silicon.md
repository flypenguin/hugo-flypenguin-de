---
title: install “legacy” brew on M1 apple silicon
author: penguin
type: post
date: 2020-12-13T19:43:09+00:00
url: /2020/12/13/install-legacy-brew-on-m1-apple-silicon/
categories:
  - Uncategorized
tags:
  - apple
  - apple silicon
  - homebrew
  - snippet

---
I tried installing homebrew for about 3 days following [the][1] [completely][2] [identical][3] [howto][4] postings out there (i.e. "prefix with 'arch -x86_64'"). Alas, it didn't work:

```
$ arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
Password:
Homebrew is not (yet) supported on ARM processors!
Rerun the Homebrew installer under Rosetta 2.
If you really know what you are doing and are prepared for a very broken
experience you can use another installation option for installing on ARM:
  https://docs.brew.sh/Installation
```

The [other recommendation][5] (run the whole terminal under Rosetta2, scroll down to "Running a special flavor [...]") also did not work.

What _did_ finally work though was changing my login shell to `/bin/bash` instead of the default `zsh` for the time being:<figure class="wp-block-image size-large">

<img loading="lazy" width="1024" height="410" src="https://flypenguin.de/wp-content/uploads/2020/12/Bildschirmfoto-2020-12-13-um-20.40.50-1024x410.png" alt="" class="wp-image-1327" srcset="https://flypenguin.de/wp-content/uploads/2020/12/Bildschirmfoto-2020-12-13-um-20.40.50-1024x410.png 1024w, https://flypenguin.de/wp-content/uploads/2020/12/Bildschirmfoto-2020-12-13-um-20.40.50-300x120.png 300w, https://flypenguin.de/wp-content/uploads/2020/12/Bildschirmfoto-2020-12-13-um-20.40.50-768x307.png 768w, https://flypenguin.de/wp-content/uploads/2020/12/Bildschirmfoto-2020-12-13-um-20.40.50.png 1040w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> 

Maybe that helps someone.

The "install the M1 version" process [described by Noah Peters][6] (thanks, really!) did work on the first try.

 [1]: https://stackoverflow.com/a/64883440/902327
 [2]: https://news.ycombinator.com/item?id=25132679
 [3]: https://soffes.blog/homebrew-on-apple-silicon
 [4]: https://github.com/Homebrew/brew/issues/9173#issuecomment-729215356
 [5]: https://jeannot.medium.com/how-to-install-homebrew-on-apple-silcon-m1-with-big-sur-1c02293f9d40
 [6]: https://noahpeeters.de/posts/apple-silicon/homebrew-setup/