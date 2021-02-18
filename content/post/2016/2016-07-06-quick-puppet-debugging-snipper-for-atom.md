---
title: Quick puppet debugging snipper for Atom
author: penguin
type: post
date: 2016-07-06T16:55:47+00:00
url: /2016/07/06/quick-puppet-debugging-snipper-for-atom/
categories:
  - Puppet
tags:
  - snippet

---
Not sure how I could have lived without this until now (had it before in Sublime, never bothered porting, stooooopid as I realize now 😉 ):

```
'.source.puppet':
  'Puppet: fail inline template':
    'prefix': 'fit'
    'body': """
      $fail_me = inline_template('<%= require "yaml"; YAML.dump(@$1) %>')
      fail("\\\\n\\\\nVariable \\\\$$1:\\\\n\${fail_me}\\\\n\\\\n")
    """
```