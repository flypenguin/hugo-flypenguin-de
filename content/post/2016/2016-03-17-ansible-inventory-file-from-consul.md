---
title: Ansible inventory file from Consul
author: penguin
type: post
date: 2016-03-17T16:09:30+00:00
url: /2016/03/17/ansible-inventory-file-from-consul/
categories:
  - Snippets
tags:
  - ansible
  - consul
  - jq

---
Quick self-reminder:

<pre>curl consul.domain:8500/v1/catalog/nodes | jq '.[]|.Address' | tr -d '"'</pre>