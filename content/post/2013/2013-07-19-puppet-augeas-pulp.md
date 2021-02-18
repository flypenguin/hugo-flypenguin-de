---
title: 'Puppet & Augeas & Pulp'
author: penguin
type: post
date: 2013-07-19T11:12:58+00:00
url: /2013/07/19/puppet-augeas-pulp/
categories:
  - Uncategorized
tags:
  - augeas
  - nugget
  - puppet

---
Ach Augeas ist schon genial. Wenn nur nicht ... (jaja, immer was zu meckern). Anlass diesmal: /etc/pulp/admin/admin.conf. Das ist eine in Augeas nicht vorgesehene Datei, und die Augeas-Doku ist ... nun ja. Analyse: Die Datei besteht aus Sektionen ("[blablubb]"), und Einträgen ("hallo = welt"). Da sollte sich doch was finden lassen.

Tut es auch: Die IniFile-Lens. Preisfrage: Wie testet man das? Beim Ausprobieren [stieß ich][1] auch auf die Information, dass die IniFile-Lens nicht für direkte Nutzung gedacht ist, sondern nur für die Nutzung in ... abgeleiteten Lenses. Wie z.B. der Puppet-Lens. Die angeblich gut passt. Dann testet man das auf der Konsole folgendermaßen:

```
augtool&gt; set /augeas/load/IniFile/lens Puppet.lns
augtool&gt; set /augeas/load/IniFile/incl /etc/pulp/admin/admin.conf
augtool&gt; load
augtool&gt; match /files/etc/pulp/admin/admin.conf/server/host
/files/etc/pulp/admin/admin.conf/server/host = localhost.localdomain
```

Schee. Total intuitiv, oder? 🙂

Ich möchte also den Wert von "host" unter "[server]" ändern, wie man sieht. Die dafür notwendige Puppet-Regel sieht so aus:

```
augeas { "admin.conf/${key}":
    incl    =&gt; '/etc/pulp/admin/admin.conf',
    lens    =&gt; 'Puppet.lns',
    onlyif  =&gt; "get /files/etc/pulp/admin/admin.conf/server/host != ${hostname}",
    changes =&gt; [
        "set /files/etc/pulp/admin/admin.conf/server/host ${hostname}",
    ],
}
```

So wird ein Schuh draus. Man beachte, dass in der onlyif-Abfrage vor und hinter "!=" ein Leerzeichen stehen muss.

 [1]: https://www.redhat.com/archives/augeas-devel/2012-March/msg00011.html