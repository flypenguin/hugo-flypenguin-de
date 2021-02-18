---
title: Nervige Puppet Fehler
author: penguin
type: post
date: 2013-07-26T11:20:02+00:00
url: /2013/07/26/nervige-puppet-fehler/
categories:
  - Uncategorized
tags:
  - puppet
  - rant

---
Was geht hier nicht?

```
class { 'whatever' :
    do     =&gt; 'something',
    before =&gt; Class[ 'something_else' ],
} -&gt;

class { 'yeah_yeah' : do =&gt; 'even_more', }
```

Na? Niemand? Gut. Lösung: "before =>" und "->" mischen sich nicht. Das wäre nicht so schlimm, wäre die Fehlermeldung nicht absolut ... unzureichend:

```
err: Could not retrieve catalog from remote server: Error 400 on SERVER: undefined method `&lt;&lt;' for {}:Hash on node bstrap_foreman_v2. ...
```

Noch sowas dass man nicht mehr vergisst. Glücklicherweise gibts grafische git logs ...