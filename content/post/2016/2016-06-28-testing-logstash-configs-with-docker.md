---
title: Testing logstash configs with Docker
author: penguin
type: post
date: 2016-06-28T16:22:29+00:00
url: /2016/06/28/testing-logstash-configs-with-docker/
categories:
  - Infrastructure
tags:
  - docker
  - logstash
  - snippet
  - testing

---
Now this is really not rocket science, but since I might do this more often, I don't want to google every time.

### Prepare your directories

<pre class="lang:sh decode:true ">./tmp                   # THIS IS YOUR WORKING DIRECTORY
  |- patterns/          # optional
  |   |- patternfile1   # optional
  |   |- patternfile2   # optional
  |- logs.log
  |- logstash.conf</pre>

### Prepare your logstash config

<pre class="lang:sh decode:true "># logstash.conf
input {
  file {
    path =&gt; '/stash/logs.log'
  }
}

filter {
  # whatever config you want to test
  grok {
    match        =&gt; [ "message", "%{WHATEVER}" ]
    patterns_dir =&gt; '/stash/patterns'              # optional :)
  }
}

output {
  stdout { codec =&gt; rubydebug }
}</pre>

### Run logstash

<pre class="lang:sh decode:true ">docker run --rm -ti -v $(pwd):/stash logstash logstash -f /stash/logstash.conf</pre>

### Done.

Done. 🙂