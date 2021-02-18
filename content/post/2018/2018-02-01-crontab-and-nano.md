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
Ever used `update-alternatives` to switch everything to vim and ... `crontab -e` _still_ used nano?

Well, I had this. I [found the answer][1]:

```default
select-editor
```



 [1]: https://askubuntu.com/a/264720