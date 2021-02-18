---
title: Oracle virtualization
author: penguin
type: post
date: 2014-01-13T14:22:00+00:00
url: /2014/01/13/oracle-virtualization/
categories:
  - Uncategorized
tags:
  - das nervt
  - oracle

---
Ich hatte das zweifelhafte Vergnügen, mich mit OracleVM beschäftigen zu dürfen. Der Xen-basierten Virtualisierungslösung von Oracle. Zu dieser gehört der sog. "Oracle Manager", eine WebLogic-basierte Anwendung zur Verwaltung aller VM hosts, cluster, VMs, und sonst noch allem. Die Aufgabe, die wir zu erledigen hatten, war prinzipiell einfach: Benutze den vorhandenen Manager, um auf 3 neuen Blades jeweils OracleVM (das Host-System) zu installieren, um dort ein paar virtuelle Maschinen laufen zu lassen. Der Trick: Die Blades waren über Trunk-Ports ans Netzwerk angeschlossen, da die VMs in verschiedenen Netzen operieren sollten. 

Und nach einer Woche testen fanden wir heraus, warum die Netz-Zuweisung mehr oder weniger zufällig funktionierte.

Denn der grandiose Manager ignoriert vollkommen eine Änderung der angeschlossenen Netze auf der "Netzwerkkarte" der VM. Um das angeschlossene Netz zu ändern muss man ... 

  * die VM herunterfahren
  * die Netzwerkkarte löschen
  * eine neue Netzwerkkarte hinzufügen (gerne auch den gleichen virtuellen Adapter wie vorher), allerdings vorher schon das passende Netzwerk aussuchen
  * die VM starten

Dann ist das Netzwerk geändert.