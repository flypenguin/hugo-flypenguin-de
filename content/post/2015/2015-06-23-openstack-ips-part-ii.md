---
title: OpenStack IPs, part II
author: penguin
type: post
date: 2015-06-23T06:44:10+00:00
url: /2015/06/23/openstack-ips-part-ii/
categories:
  - Uncategorized
tags:
  - nugget
  - openstack

---
Just a short one. Now that I can list IPs (see last post), it might be nice to associate them on the command line, too. First I wanted to extend the little script, but then I remembered there must already be a CLI way for this.

And there is.

```
$ openstack server list
$ nova floating-ip-associate <server-uuid> <floating-IP>
```

(_Note:_ The floating-ip is the actual IP, not the UUID _of_ the OpenStack floating IP)

Groovy.