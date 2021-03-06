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

```sh
./tmp                   # THIS IS YOUR WORKING DIRECTORY
  |- patterns/          # optional
  |   |- patternfile1   # optional
  |   |- patternfile2   # optional
  |- logs.log
  |- logstash.conf
```

### Prepare your logstash config

```sh
# logstash.conf
input {
  file {
    path => '/stash/logs.log'
  }
}

filter {
  # whatever config you want to test
  grok {
    match        => [ "message", "%{WHATEVER}" ]
    patterns_dir => '/stash/patterns'              # optional :)
  }
}

output {
  stdout { codec => rubydebug }
}
```

### Run logstash

```sh
docker run --rm -ti -v $(pwd):/stash logstash logstash -f /stash/logstash.conf
```

### Done.

Done. 🙂