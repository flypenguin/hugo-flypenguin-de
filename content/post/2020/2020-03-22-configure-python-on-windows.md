---
title: Configure Python on Windows
author: penguin
type: post
date: 2020-03-22T08:32:03+00:00
url: /2020/03/22/configure-python-on-windows/
categories:
  - Snippets
tags:
  - python
  - windows

---
All right, I have a Windows machine. It's a PITA, but it's here. And for some reason I started doing some Python testing on it. So this is how I managed to do it:

Preparation:

  * Install python with [choco][1] (<code class="EnlighterJSRAW" data-enlighter-language="null">choco install -y python</code>)
  * Run PowerShell as Administrator 
      * Execute <code class="EnlighterJSRAW" data-enlighter-language="null">Set-ExecutionPolicy -ExecutionPolicy Unrestricted</code> (we'll see why in a very short time)

Now to code it's pretty similar to *NIX:

  * Create your code folder
  * Set up a python venv (<code class="EnlighterJSRAW" data-enlighter-language="null">python -m venv .env</code>)
  * In VS Code, choose this interpreter

So why the PowerShell stuff? Cause to _activate_ the environment VS Code needs to execute a .ps1 script. Which it can't, cause "executing scripts is disabled on this machine", which seems to be the default setting.

All in all, surprisingly straightforward. And I just noticed even the *NIX keyboard shortcuts (CTRL-A, CTRL-K, for example) work in the terminal window now. Crazy.

 [1]: https://chocolatey.org/