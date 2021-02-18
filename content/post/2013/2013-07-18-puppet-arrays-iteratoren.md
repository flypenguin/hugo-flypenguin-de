---
title: 'Puppet, Arrays & Iteratoren'
author: penguin
type: post
date: 2013-07-18T16:05:48+00:00
url: /2013/07/18/puppet-arrays-iteratoren/
categories:
  - Uncategorized
tags:
  - puppet
  - rant

---
Endlich, endlich, endlich kommt in Puppet 3.2 die Möglichkeit, Schleifen zu bauen. Dann könnte ich eventuell folgende Aufgabenstellung ein klein wenig einfacher realisieren (aktuell arbeite ich bis zur endgültigen Umstellung unserer Systeme mit Puppet 2.7):

> Fasse alle im Rechner befindlichen Blockdevices der Form "/dev/sd*" - aber außer /dev/sda - in einer LVM volume group zusammen.

Das Herausfinden der Blockdevices erledigt ein Fact aus Facter ... naja, nicht ganz - ein [in Facter 2.0 verfügbarer Fact][1], den ma aber dankenswerterweise zurückportieren kann. Dieser liefert uns $blockdevices - eine Komma-getrennte Liste der gefundenen devices, allerdings ohne "/dev/", also nur "sda,sdb,sdc".

Das hinzufügen, und dann ... ja, was dann? In Ruby kein Thema, aber jetzt möchte ich von "sda,sdb,sdc" zu ["/dev/sda", "/dev/sdb", "/dev/sdc"].

Und das geht so:

```
<span style="font-family:sans-serif;font-size:small;"># next line broken up for readability
$devices_tmp    = inline_template( '
    &lt;%= devs=scope.lookupvar("::blockdevices")
        .split(",")
        .select{|f| ! f.include? "sda" }
        .select{|f| f.include? "sd" }
        .collect{|f| "/dev/" + f}.join "," %&gt;' )

# here we split the string back to an array. 
$devices_wanted = split($devices_tmp, ",")

# but it works.
# isn't life shit?
notify { $devices_wanted : }</span>
```

Schön ist anders. Falls jemand eine bessere Idee hat - immer her damit ...

 [1]: https://github.com/puppetlabs/facter/pull/98