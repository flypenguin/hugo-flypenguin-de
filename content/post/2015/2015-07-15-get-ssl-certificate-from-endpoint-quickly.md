---
title: Get SSL certificate from endpoint quickly
author: penguin
type: post
date: 2015-07-15T14:17:02+00:00
url: /2015/07/15/get-ssl-certificate-from-endpoint-quickly/
categories:
  - Snippets
tags:
  - helpful

---
If you want to download and save it so <something> stops f*cking bugging you, you need to have it.

Here's a quick way. Shamelessly stolen from here: http://is.gd/A11rNR

```
openssl s_client -host google.com -port 443 -prexit -showcerts
```