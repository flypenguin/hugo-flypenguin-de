---
title: Fedora, docker and self-signed SSL certs
author: penguin
type: post
date: 2015-04-15T06:48:58+00:00
url: /2015/04/15/fedora-docker-and-self-signed-ssl-certs/
categories:
  - Uncategorized
tags:
  - docker
  - fedora
  - general
  - rhel
  - ssl

---
I am behind a company firewall with a man-in-the-middle SSL certificate for secure connections. Can't have viruses over SSL, can we?

But apps which actually verify SSL connections (which is all of the apps using standard SSL/TLS/whatnot libs) do not like this. And rightfully so. But then we're left with the following problem:

<pre>$ docker search test
FATA[0000] Error response from daemon: GEt https://index.docker.io/v1/search?q=test: x509: certificate signed by unknown authority
$</pre>

Now, to solve this on Fedora we do the following (all as root):

  * get a file with the signing certificate as PEM or DER format
  * place this file under /etc/pki/ca-trust/source/anchors
  * run "update-ca-trust extract"
  * restart docker ("systemctl restart docker.service")

A "man update-ca-trust" is also helpful to understand what's happening.

Sources:

  * http://is.gd/rzKxsO
  * http://is.gd/t9to1R