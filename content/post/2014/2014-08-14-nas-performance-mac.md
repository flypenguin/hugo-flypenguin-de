---
title: 'NAS Performance & Mac'
author: penguin
type: post
date: 2014-08-14T09:08:31+00:00
url: /2014/08/14/nas-performance-mac/
categories:
  - Uncategorized

---
Ich bin stolzer Besitzer eines Synology NAS. Das hängt mit meinem Mac an einem Gigabit-Ethernet, und funktioniert ziemlich gut. Große Dateien bekomme ich mit Transferraten von +- 100 MB/sec rüber.

Aber. 

Möchte ich von meinem Mac aus eine Netzwerkfreigabe im Finder browsen - dann dauert das listen von Verzeichnissen SEKUNDEN. Anzeigen von JPGs (kleinen!) in der Vorschau auch gerne mal 10-20 davon.

Abhilfe schafften nach langem googlen folgende Erste-Hilfe-Maßnahmen im Terminal:

`<br />
defaults write com.apple.desktopservices DSDontWriteNetworkStores true<br />
sudo sysctl -w net.inet.tcp.delayed_ack=0<br />
` 

... und seitdem flutscht's 😀