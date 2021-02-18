---
title: Host monitoring with Prometheus
author: penguin
type: post
date: 2016-03-08T20:38:13+00:00
excerpt: How to monitor your host metrics with Prometheus, using consul to automatically pick up freshly spawned hosts.
url: /2016/03/08/host-monitoring-with-prometheus/
client-modified:
  - 1457470947
categories:
  - Infrastructure
tags:
  - consul
  - monitoring
  - prometheus
  - puppet

---
I needed monitoring. The plan was to go for an external service - if our environment breaks down, the monitoring is still functional (at least as far as the remaining environment goes). I started to evaluate [sysdig cloud][1], which comes [somewhat recommended][2] from "the internet".

But then I was kinda unsatisfied (to be honest - most probably unjustified) with the service, because I really didn't like the UI, and then one metric which was displayed was just _wrong_. So I got back to [prometheus][3], which we use for metrics gathering of our running services anyway, and used it for host metric monitoring, too.

That's my setup. (sorry for the crappy graphic, WordPress does not support SVG ... ?!?)

<img loading="lazy" class="alignnone size-full wp-image-692" src="https://sysadminia.files.wordpress.com/2016/03/monitoring-setup.png" alt="Monitoring setup.png" width="706" height="293" srcset="https://flypenguin.de/wp-content/uploads/2016/03/monitoring-setup.png 706w, https://flypenguin.de/wp-content/uploads/2016/03/monitoring-setup-300x125.png 300w" sizes="(max-width: 706px) 100vw, 706px" /> 

Cause I have consul running on every host, puppet deploying everything, I can use puppet to register the exporter services to consul, and consul to [configure prometheus][4], which has [native consul support][5].

The prometheus configuration to pull all this is pretty simple actually, once it works:

<pre>global:
  scrape_interval: 10s
  scrape_timeout: 3s
  evaluation_interval: 10s
scrape_configs:
  - job_name: consul
    consul_sd_configs:
      - server: consul.internal.net:8500
        services: [prom-pushgateway, cadvisor, node-exporter]
    relabel_configs:
      - source_labels:  ['__meta_consul_node']
        regex:          '^(.*)$'
        target_label:   node
        replacement:    '$1'
      - source_labels:  ['__meta_consul_service']
        regex:          '^(.*)$'
        target_label:   job
        replacement:    '$1'
    metric_relabel_configs:
      - source_labels:  ['id']
        regex:          '/([^/]+)/.*'
        target_label:   item_type
        replacement:    '$1'
      - source_labels:  ['id']
        regex:          '/[^/]+/(.*)'
        target_label:   item
        replacement:    '$1'
      - source_labels:  ['id']
        regex:          '/docker/(.{8}).*'
        target_label:   item
        replacement:    '$1'</pre>

Some caveats:

  * Prometheus will not tell you why a relabeling does not work. It will just not do it.
  * Prometheus will not tell you that a regex is faulty on SIGHUP, only on restart.
  * The difference between "metric\_relabel\_configs" and "relabel\_configs" seems to be that the former must be applied to scraped metrics, while the latter can only be applied to metrics which are "already present", which seems to be only the "\\_\_*"-meta labels (for example "\_\_meta\_consul\_service")

Then it works like a charm.

And the final bonbon: Directly after I had it running I discovered a problem:

<img loading="lazy" class="alignnone size-full wp-image-706" src="https://sysadminia.files.wordpress.com/2016/03/cdcvlpnwaaarheg.jpg" alt="CdCVlPNWAAArHEg.jpg" width="833" height="215" srcset="https://flypenguin.de/wp-content/uploads/2016/03/cdcvlpnwaaarheg.jpg 833w, https://flypenguin.de/wp-content/uploads/2016/03/cdcvlpnwaaarheg-300x77.jpg 300w, https://flypenguin.de/wp-content/uploads/2016/03/cdcvlpnwaaarheg-768x198.jpg 768w" sizes="(max-width: 833px) 100vw, 833px" /> 

Yippieh 😀

#consul, #monitoring, #prometheus, #puppet

 [1]: https://sysdig.com/
 [2]: http://rancher.com/docker-monitoring-continued-prometheus-and-sysdig/
 [3]: http://www.prometheus.io
 [4]: https://prometheus.io/docs/operating/configuration/#%3Cconsul_sd_config%3E
 [5]: https://prometheus.io/blog/2015/06/01/advanced-service-discovery/#discovery-with-consul