---
title: Docker and proxies
author: penguin
type: post
date: 2015-07-15T13:59:43+00:00
url: /2015/07/15/docker-and-proxies/
categories:
  - Docker
tags:
  - docker

---
... so _I_ don't forget.

"docker pull" will not use the HTTP_PROXY variable. Why? Because "docker" is just the cli program which tells the _daemon_ what to do. And the daemon probably does not know about the variable if just set in the terminal.

So, what to do to make docker use it described pretty well here: <https://docs.docker.com/articles/systemd/#http-proxy>

Next thing: Don't forget to go "systemctl daemon-reload", because otherwise this will not be effective, even with "systemctl restart docker".

Done.