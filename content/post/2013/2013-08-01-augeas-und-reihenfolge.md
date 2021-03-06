---
title: Augeas und Reihenfolge
author: penguin
type: post
date: 2013-08-01T16:58:13+00:00
url: /2013/08/01/augeas-und-reihenfolge/
categories:
  - Uncategorized
tags:
  - augeas
  - puppet

---
Problem: mit HIlfe von Augeas einen Eintrag zur /etc/hosts Datei hinzufügen.

Erste Lösung:

```default
augeas { "${title}" :
    context => '/files/etc/hosts',
    changes => [
        "rm *[canonical = '${host}']",
        "set 02/canonical ${host}",
        "set 02/ipaddr ${ip}",
    ]
}
```

Funktioniert nicht. Warum? Trotz Aufruf von "save" am Ende einer jeden Sitzung im augtool ist die Reihenfolge der Anweisungen durchaus entscheidend - die Daten werden offenbar nicht erst am Ende zusammengesetzt. Hier z.B. gilt: In der gleichen Reihenfolge vorgehen, wie es auch in die Datei geschrieben werden würde. Und da steht nun mal am Anfang die IP Adresse. Daher also eine einfache kleine Änderung machen und schon gehts:

```default
augeas { "${title}" :
    context => '/files/etc/hosts',
    changes => [
        "rm *[canonical = '${host}']",
        "set 02/ipaddr ${ip}",
        "set 02/canonical ${host}",
    ]
}
```

(Ganz davon abgesehen dass man einfach den host{} Typen von Puppet verwenden sollte)