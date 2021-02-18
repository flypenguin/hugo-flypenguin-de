---
title: crontab and nano
author: penguin
type: post
date: 2018-02-01T08:55:18+00:00
url: /2018/02/01/crontab-and-nano/
categories:
  - Snippets
tags:
  - commandline
  - linux

---
Ever used <span class="lang:default decode:true  crayon-inline">update-alternatives</span> to switch everything to vim and ... <span class="lang:default decode:true  crayon-inline">crontab -e</span> _still_ used nano?

Well, I had this. I [found the answer][1]:

```default
select-editor
```

&nbsp;

 [1]: https://askubuntu.com/a/264720