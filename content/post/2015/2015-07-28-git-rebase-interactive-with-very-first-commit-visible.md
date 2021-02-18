---
title: Git rebase interactive with very first commit visible
author: penguin
type: post
date: 2015-07-28T14:34:08+00:00
url: /2015/07/28/git-rebase-interactive-with-very-first-commit-visible/
categories:
  - Snippets
tags:
  - git
  - helpful

---
Ever wanted to squash the first two commits with a "git rebase -i"? No? Me neither. Until today.

[Stackoverflow to the rescue][1]:

```
git rebase -i --root
```

Done.

 [1]: http://stackoverflow.com/questions/598672/squash-the-first-two-commits-in-git