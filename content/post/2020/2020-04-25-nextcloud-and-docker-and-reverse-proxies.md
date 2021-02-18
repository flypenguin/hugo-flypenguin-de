---
title: nextcloud and Docker and reverse proxies
author: penguin
type: post
date: 2020-04-25T23:18:15+00:00
url: /2020/04/25/nextcloud-and-docker-and-reverse-proxies/
categories:
  - Snippets
  - Uncategorized
tags:
  - annoying
  - docker
  - hosting
  - nextcloud

---
I have a nextcloud setup like [described here][1] (docker-compose, let's encrypt proxy companion, postgres and nextcloud). And for a while I couldn't connect any new nextcloud clients to the installation.

This fixed it:

```
<?php
$CONFIG = array (
  # manually added because it's not picked up from
  # the env vars once set ... it seems ...

  # the docker IP range
  'trusted_proxies' => ["172.16.0.0/12"],

  # the hostname of the server
  'overwritehost'   => "my.super.secret.server",

  # the ENDUSER->PROXY protocol, NOT the proxy-> nextcloud protocol!
  'overwriteprotocol' => "https",

  #
  # AAAND NOW back to the original config file ...
  # ...
)
```

Some notes:

  * You have to [add this directly to config.php][2], because once the container has started _once_ [the env vars][3] have no effect
  * Restarting the client helps as well.

 [1]: https://github.com/nextcloud/docker/blob/master/.examples/docker-compose/with-nginx-proxy/postgres/apache/docker-compose.yml
 [2]: https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/reverse_proxy_configuration.html
 [3]: https://github.com/nextcloud/docker#using-the-apache-image-behind-a-reverse-proxy-and-auto-configure-server-host-and-protocol