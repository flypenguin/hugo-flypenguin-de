---
title: Puppet spec fixtures
author: penguin
type: post
date: 2015-07-28T12:37:10+00:00
url: /2015/07/28/puppet-spec-fixtures/
categories:
  - Puppet
tags:
  - puppet
  - testing

---
That's how you specify branches in puppetlabs' spec_helper fixtures.yml:

<pre>fixtures:
  forge_modules:
    stdlib:
      repo: "puppetlabs/stdlib"
      ref: "4.6.0"
  symlinks:
    mapr_helpers: "#{source_dir}"
  repositories:
    mapr:
      repo: "http://myurl/puppet-mapr.git"
      # this is a branch. stolen from: don't remember.
      ref: 'remotes/origin/ab/devel'
</pre>