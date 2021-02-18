---
title: Homebrew, OpenSSL and PowerShell
author: penguin
type: post
date: 2020-05-06T10:23:54+00:00
url: /2020/05/06/homebrew-openssl-and-powershell/
categories:
  - Snippets
tags:
  - annoying
  - powershell

---
On Mac, PowerShell [really][1], [really][2] wants to specifically use OpenSSL version 1.0.

Unfortunately, [homebrew switched to OpenSSL version 1.1 in v2.2][3], because OpenSSL 1.0 is end-of-life.

[This fixes it][4] (for now at least):

<pre class="EnlighterJSRAW" data-enlighter-language="shell">brew uninstall openssl --ignore-dependencies
brew uninstall openssl --ignore-dependencies
brew install https://github.com/tebelorg/Tump/releases/download/v1.0.0/openssl.rb</pre>

References:

  * [My comment on GitHub][5]

 [1]: https://github.com/PowerShell/PowerShell/issues/5561#issuecomment-610086969
 [2]: https://www.google.com/search?client=firefox-b-d&q=powershell+wsman+client+not+found
 [3]: https://brew.sh/2019/11/27/homebrew-2.2.0/
 [4]: https://github.com/kelaberetiv/TagUI/issues/635#issuecomment-560139279
 [5]: https://github.com/PowerShell/PowerShell/issues/11216#issuecomment-624563790