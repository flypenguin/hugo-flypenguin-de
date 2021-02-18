---
title: OpenStack floating IP convenience
author: penguin
type: post
date: 2015-06-22T10:35:12+00:00
url: /2015/06/22/openstack-floating-ip-convenience/
categories:
  - Snippets
tags:
  - convenience
  - openstack
  - python

---
Problem: I am working in a tenant which has a couple of hosts with floating IPs assigned. I always have to look them up either manually using the command line clients (and dealing with all those UUIDs), or manually in the web GUI. Didn't like.

Solution: Python script, which outputs FLOATING\_IP -> HOST\_NAME.

Here it is.

```
#!/usr/bin/env python

from novaclient import client
import novaclient.v2.floating_ips as os_fips
import novaclient.v2.servers as os_servers
import novaclient.v2.networks as os_networks

#from pprint import pprint as pp
import os
from sys import exit


def error(printme):
    print("ERROR: {}".format(printme))
    exit(-1)

def check_env():
    for a in ("OS_TENANT_ID", "OS_TENANT_NAME", "OS_AUTH_URL", "OS_USERNAME", "OS_PASSWORD"):
        if not os.environ.get(a): error("Please set ${}".format(a))

def get_client():
    check_env()
    return client.Client(2,
                         os.environ["OS_USERNAME"],
                         os.environ["OS_PASSWORD"],
                         os.environ["OS_TENANT_NAME"],
                         os.environ["OS_AUTH_URL"])


if __name__=="__main__":
    nova = get_client()
    fipman = os_fips.FloatingIPManager(nova)
    servman = os_servers.ServerManager(nova)
    netman = os_networks.NetworkManager(nova)

    ips = fipman.list()
    srs = servman.list()

    id2server = {}
    for a in srs:
        id2server[a.id] = a

    ips = [(ip.ip, ip.instance_id) for ip in ips]
    # filter out unused floating ips (which have  as instance id)
    ips = filter(lambda x: x[1], ips)
    # create (IP, SERVER_NAME pairs)
    ips = map(lambda x: (x[0], id2server[x[1]].name), ips)
    # sort for convenience by host instance name
    ips = sorted(ips, key=lambda x: x[1].lower())

    for a in ips:
        print("{:18s} {}".format(a[0], a[1]))
```

Sample output:

```
$ tools/list_ips
10.64.108.163      ab_1695_1_dml
10.64.108.199      ab_1695_1_jump
10.64.108.160      media0
10.64.109.97       RJD1_CouchbaseA1
10.64.109.99       RJD1_JumpServer
10.64.109.98       RJD1_OpenshiftMaster
10.64.109.148      tla-centos66
```

Groovy.

* * *

Main sources:

  * <http://docs.openstack.org/developer/python-novaclient/>
  * <http://docs.openstack.org/developer/python-novaclient/api.html>