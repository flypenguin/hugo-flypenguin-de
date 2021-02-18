---
title: The limits of puppetDB a.k.a. etcd with puppet
author: penguin
type: post
date: 2014-05-09T23:15:51+00:00
url: /2014/05/09/the-limits-of-puppetdb-a-k-a-etcd-with-puppet/
categories:
  - Uncategorized
tags:
  - etcd
  - puppet
  - puppetdb
  - solution

---
Okay. Maybe this is the completely wrong way to do this, but I found no other. Comments appreciated.

Situation:

  * A DNS server cluster, meaning pacemaker managing an IP failover if one of them fails.
  * The cluster IP (the one switching over) should be the IP used for DNS lookups.

Idea:

  * Hosts should automatically find 'their' DNS server using something. The DNS server should register itself somewhere so it is automatically set up during the first puppet run.

So far, so good. Unfortunately there are a few problems with it. My first approach was something like this:

```
## THIS WILL NOT WORK

# on every DNS server (*)
@@resolv_conf_entry { $my_cluster_ip :
    tag = $domain,
}

# on every other machine:
Resolv_conf_entry <<| tag == $domain |>>
```

Unfortunately this _will_ lead to a "duplicate declaration" on the client side - because _all_ of the clustered DNS servers will export this, and the `cluster_ip` is identical on the DNS server hosts.

The solution I came up with is [etcd][1]. A project from the girls and guys of [CoreOS][2], which is basically a REST powered key-value store as trimmed down as possible, a binary about 3 MB, cluster-able, super-super-super simple to set up, and stand alone. The whole setup is like this:

  * etcd is installed on every puppet master
  * I wrote an etcd module(**) providing the methods `etcd_set_value($etcd_url,$key_name,$key_value)` and `etcd_get_value($etcd_url,$key_name)`
  * The `$etcd_url` is entered into hiera

Now, we can do the following:

```
# on each DNS server:
$etcd_url = hiera('etcd_url')
etcd_set_value( $etcd_url , &quot;/puppet/dns_server_for/${domain}&quot; , $my_cluster_ip )

# on each other server:
$etcd_url = hiera('etcd_url')
$dns_server = etcd_get_value( $etcd_url , &quot;/puppet/dns_server_for/${domain}&quot; )
resolv_conf_entry { $dns_server : }
```

Works beautifully.

Now go read the [etcd][1] docs and try to think of some other applications. etcd is a great tool (CoreOS is not too bad either, we're evaluating it right now and are pretty amazed), and go use it. It is really much simpler than puppet.

(*) Note that `resolv_conf_entry{}` is just a placeholder and does not exist (unless you write it).

(**) I cannot - unfortunately - publish the module (yet), because the customer I wrote it for has not yet allowed me to.

 [1]: https://github.com/coreos/etcd
 [2]: https://coreos.com/