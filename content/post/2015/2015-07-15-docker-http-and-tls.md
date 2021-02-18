---
title: Docker, http and TLS
author: penguin
type: post
date: 2015-07-15T15:32:12+00:00
url: /2015/07/15/docker-http-and-tls/
categories:
  - Docker
tags:
  - annoying

---
Today seems to be "annoyme-day".

This error message with docker:

<pre>$ docker push myrepo.com:5000/name/image
Post http:///var/run/docker.sock/v1.19/images/myrepo.com:5000 \
   /name/image/push?tag=: read unix /var/run/docker.sock: \
   connection reset by peer. Are you trying to connect to \
   a TLS-enabled daemon without TLS?
</pre>

... does not _necessarily_ mean that we use http:// instead of https://.

It can also mean that the docker service is not running:

<pre>$ systemctl status docker.service
 ● docker.service - Docker Application Container Engine
   Loaded: loaded (/etc/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: failed (Result: start-limit) since Wed 2015-07-15 17:16:48 CEST; 15s ago
     Docs: https://docs.docker.com
  Process: 48587 ExecStart=/usr/bin/docker td -H fd:// $DOCKER_OPTS (code=exited, status=1/FAILURE)
 Main PID: 48587 (code=exited, status=1/FAILURE)
</pre>

Took me 15 minutes.