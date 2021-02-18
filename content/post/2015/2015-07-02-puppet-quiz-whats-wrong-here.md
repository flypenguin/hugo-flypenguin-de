---
title: 'Puppet Quiz: What’s wrong here?'
author: penguin
type: post
date: 2015-07-02T09:49:23+00:00
url: /2015/07/02/puppet-quiz-whats-wrong-here/
categories:
  - Puppet
tags:
  - errors
  - puppet
  - quiz

---
The error is: Dependency cycle.

The code is:

<pre>class my::own::docker {
    include ::docker
    file { '/var/lib/docker':
        ensure =&gt; directory,
        before =&gt; Class['docker'],
    }
}</pre>

Why? 🙂

It's rather simple here, in the real class it really took me a while to find it.