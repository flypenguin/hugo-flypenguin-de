---
title: No password prompts for GitHub “hub” client
author: penguin
type: post
date: 2017-06-12T13:47:04+00:00
url: /2017/06/12/github-hub-cli-passwords/
categories:
  - General things
tags:
  - cli
  - git
  - github

---
GitHub has a little cli tool called "hub" which makes working with repos a bit easier ("hub pull-request" instead of a lot of clicking around on the web). Unfortunately they don't really tell you how to prevent password inputs every time you use it.

Unless you dig deep:

  * [Create this config file][1]
  * [... using the token created here][2]

 [1]: https://github.com/github/hub/issues/978#issuecomment-131894148
 [2]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/