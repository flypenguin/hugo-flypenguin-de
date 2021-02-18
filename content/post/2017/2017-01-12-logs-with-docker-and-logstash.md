---
title: Logs with docker and logstash
author: penguin
type: post
date: 2017-01-12T13:31:09+00:00
url: /2017/01/12/logs-with-docker-and-logstash/
categories:
  - Docker
  - Infrastructure
  - Snippets
tags:
  - docker
  - logging
  - logstash
  - ops

---
It would be nice to have all container logs from a docker cluster sent to ... let's say, an ELK stack. Right?

Right.

So we did:

  * on each host in the cluster, we use the GELF log driver to send all logs to a logstash instance
  * the logstash instance clones each request using type "ELK"
  * to the "ELK" clone, it adds the token for the external ELK service
  * the "ELK" clone goes out to the external ELK cluster
  * the original event goes to S3.

Here's how.

<!--more-->

### Some thoughts about choosing GELF over the others

Logstash sees a stream of lines. When the data is sent to it, logstash gets a lot of different data types, depending what software runs in the containers. So to filter out a container / service / instance in Kibana, we need _at least_ the container image name to say "give me the logs of _this_ service type", and then we don't even separate by different stages.

GELF gives us this information out-of-the-box:

```default
{
       "source_host" =&gt; "10...",
             "level" =&gt; 6,
           "created" =&gt; "2017-01-09T13:41:06.039364628Z",
           "message" =&gt; "This is da log!",
           "version" =&gt; "1.1",
           "command" =&gt; "./run.sh",
              "tags" =&gt; [],
        "image_name" =&gt; "hub/repo/image",
        "@timestamp" =&gt; 2017-01-12T11:56:34.395Z,
    "container_name" =&gt; "ze-container",
              "host" =&gt; "ip-10-9-8-7",
          "@version" =&gt; "1",
               "tag" =&gt; "",
          "image_id" =&gt; "sha256:...",
      "container_id" =&gt; "..."
}
```

This is a lot of information what is useful later.

Compared to that, [**syslog**][1] looks rather bleak (all according to the examples on the docker pages):

```default
Aug  7 18:33:19 HOSTNAME docker/hello-world/foobar/5790672ab6a0[9103]: Hello from Docker.
```

Yes, you can tag that, but you also _have_ to. Not with GELF. Also it's already beautifully structured in fields, whereas with syslog you'd probably have to agree on a scheme and deconstruct later.

Over to [**fluentd**][2]:

```default
2015-09-01 15:10:40 -0600 docker.3fd8678d487e: \
    {"source":"stdout","log":"Hello Fluentd!",\
     "container_id":"3fd8678d487e540c7a303e1613101e746c5012f3317434eda93f24351c1928f7",\
     "container_name":"/angry_kalam"}
```

This is ... not so helpful. _Especially_ the container name and the container ID are super useless, because there's no way I can get the image name information out of it. You _might_ be able to tag accordingly, but the same now applies as with syslog (except here we already have a document structure it seems). I would have liked to use fluentd, because there's a low-resource agent on each host, and we don't need a central collector (i.e. logstash) for lossy UDP traffic. But that made me choose GELF for the first try.

**Cloudwatch** now only logs into buckets, which is a big bunch of text. If you want to separate you have to create a cloudwatch bucket for everything, so pretty much one for every dimension (host, container name, ...). Either I have not fully understood this, or this is really really useless.

### Now, the story

_(Disclaimer: I am fairly new to the whole ELK ecosystem, so this is all based in my potentially limited knowledge, and might be wrong in places)_

We committed ourselves to just use JSON formatted log output for our log messages. Now, not every program is without flaws, so what we usually have is a couple of stack traces mixed with the output, which are also usually multi-line (read: [multiline events][3] in ELK speak).

**So one of my biggest requirements was: To parse the JSON logs as JSON when I have one, and to re-assemble several distinct log messages which form a multi-line event into one.**

The JSON-messages are not a problem. Check whether something starts and stops with a curly brace, and you're good to go. And it's always one single line anyway.

The _multiline_ messages though ... . From logstash perspective, there's just a stream of lines. For each line the information of the container is present, but for logstash that's just dumb metadata without any meaning. So imagine this sequence of events:

```default
EVENT 0: CONTAINER_1_HOST_1 JSON-MESSAGE-LINE
EVENT 1: CONTAINER_2_HOST_2 JSON-MESSAGE-LINE
EVENT 2: CONTAINER_3_HOST_1 MULTILINE LOG - LINE 0
EVENT 3: CONTAINER_4_HOST_3 JSON-MESSAGE-LINE
EVENT 4: CONTAINER_1_HOST_1 MULTILINE LOG - LINE 0
```

If I naively use the multiline _codec_ like this:

```default
input {
  stdin {
    codec =&gt; multiline {
      pattern =&gt; "^\s"
      what =&gt; "previous"
    }
  }
}
```

I guess that would not work. Why? Because either Event 4 gets appended to the line of Event 3, which is wrong, or (I have no clue how the codecs work) to Event 2, which is equally wrong (different container source).

Now, the solution to use the [GELF input with a multiline codec does not seem to work][4], because there is a [bug in logstash which prevents usage of codecs in the GELF input][5]. Luckily there's a [workaround][6] using the multiline input filter of logstash, outside of the plugin. Fortunately there's an input plugin we can use, called multiline (yes, just like the filter plugin), [which seems to be working][7]. Let's try this. _Unfortunately_ this plugin is [no longer bundled with Logstash 5.x][8]. (Sucks? Yes.) It was "[replaced][9]" in favor of the [multiline codec][10], which reads like "deprecated" to me, although I get the motivation. But it makes me feel weird using it. (Sucks even more? Yes.)

To add insult to injury I deploy logstash using Docker, and the Docker image now does not have this plugin - so I have to build my own logstash image. I don't like to repeat myself, so I built a logstash image which can dynamically install plugins. Adds ages to startup time, but if I ever have another plugin I need I don't need to rebuild the image, just restart.

> [ [Our Logstash dynamic plugin docker container on quay.io][11] ]

Our test-config for now, which seems to be working well so far (couple of test runs only), is:

```default
input { gelf {} }

filter {

  # if we have json, let's use it.
  if [message] =~ /^{.*}$/ {
    json {
      source =&gt; 'message'
      target =&gt; 'data'
    }

    mutate {
      remove_field =&gt; 'message'
    }
  }

  multiline {
    pattern =&gt; '^\s'
    what =&gt; 'previous'
    stream_identity =&gt; "%{container_id}"
  }

  # let's clone and add the token field. the ORIGINAL input goes to S3,
  # the CLONED input with the token field goes to logz.io

  clone {
    clones =&gt; [ "one" ]
    add_field =&gt; { "token" =&gt; "asdf" }
  }

}

output { # ... }
```

That works somewhat well. All in all, I am pretty pleased after the whole thing, because once you have overcome the obstacles of the logstash config language, plugins, docker configuration, etc., it is running pretty smoothly.

Now, I still don't like this solution for the following reasons:

  * Apparently a huge performance impact if we do this, but this is IMHO the only real solution to the problem - except doing crappy stuff and then maintaining it.
  * It might be that the fluentd-based system is more robust (no single point of failure on _my_ side) and better fitting for the task (multiline, no **lossy** UDP, which might be a problem in some use cases).
  * Now even if I _could_ use the codec instead of the filter, it does not come with a "stream_identity" parameter. Whether you don't need it any more (then how does it do it?!) or it went without replacement I don't know.
  * I have to scale up (anyways) - but I do not have any idea about UDP package loss and resource requirements on the logstash side yet, and when something happens logs will be lost. This is rather unfortunate.
  * Java stack traces are fine. Python and Go stack traces not so much - they contain non-indented lines within the body of the stack trace, so the "starts with whitespace? add to last line" approach does not work. And I have no idea for other approaches to be honest.
  * Changing logstash configs promises to be a pain - because it's central component, so doing this without logs getting lost you seem to need to ...
      * have multiple logstash instances, load-balanced using IP round robin probably, with at least one more than you need for the full load
      * Take one of those instances out of the round-robin
      * Wait until traffic is exactly down to zero (which might take _time_), ...
      * adjust the rules, ...
      * test somehow (takes quite a while because of logstash start times, and if you have to test multiple times this is quite annoying) ...
      * and save.
      * Then re-add the instance-IP to the IP pool, ...
      * ... and repeat for all the rest.

I hope I am doing a couple of things "wrong", because as long as it runs it runs fine, but changes are not so agile in this description. I still might check out fluentd in more detail, because if that workflow is needed fluentd might be a bit more flexible.

But all in all a really great first start.

 [1]: https://docs.docker.com/engine/admin/logging/overview/#/syslog-options
 [2]: https://docs.docker.com/engine/admin/logging/fluentd/
 [3]: https://www.elastic.co/guide/en/logstash/current/multiline.html
 [4]: https://github.com/elastic/logstash/issues/4308
 [5]: https://github.com/logstash-plugins/logstash-input-gelf/issues/37
 [6]: https://github.com/elastic/logstash/issues/4308#issuecomment-223920111
 [7]: http://stackoverflow.com/a/34659306/902327
 [8]: https://www.elastic.co/guide/en/logstash/5.0/breaking-changes.html#_logstash_with_all_plugins_download
 [9]: https://twitter.com/elastic/status/819668016149372928
 [10]: https://www.elastic.co/guide/en/logstash/5.1/plugins-codecs-multiline.html
 [11]: https://quay.io/repository/c11h/logstash