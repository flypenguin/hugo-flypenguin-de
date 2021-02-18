---
title: zsh and dot-directory completions
author: penguin
type: post
date: 2014-08-27T18:29:15+00:00
url: /2014/08/27/zsh-and-dot-directory-completions/
categories:
  - Uncategorized
tags:
  - '*nix'
  - remember
  - zsh

---
I blog so I don't forget. At least that's what I'm telling me right now 🙂 .

So. To make zsh complete - for example - "cd .." (it should append a "/" on TAB, right?) set the following in the .zshrc:

<pre>setopt autocd<br />zstyle ':completion:*' special-dirs true    # please complete "cd .._/_" ...</pre>

Great. Of course from [Stackoverflow][1].

 [1]: http://bit.ly/1tVYw7g