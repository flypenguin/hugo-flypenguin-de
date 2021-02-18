---
title: The year of … the Installation.
author: penguin
type: post
date: 2013-06-24T10:11:32+00:00
url: /2013/06/24/the-year-of-the-installation/
categories:
  - Uncategorized
tags:
  - arch
  - desktop
  - linux
  - rant
  - system

---
Wie versprochen - ein klein wenig gemecker über die Linux Desktop Bemühungen. Erste Hürde: **Die Installation**.

Meine Wahl fiel auf Arch Linux, das sollte gut sein, und ich wollte nicht auf den Ubuntu-Zug aufspringen. Bedingungen: Vollverschlüsselte Festplatte (incl. Swap und System), verlangt der Kunde. Ist prinzipiell gut dokumentiert, auch durch eine Howto eines Kollegen. Aber ich wollte statt LVM auf Partition mal BTRFS testen - wenn schon denn schon.

Um es kurz zu machen - die Installation dauerte knapp 3 Tage.

Zuerst bootete das System nicht. Grub 2 wollte partout nicht starten und hing - auch nach mehrfachem neu schreiben des Bootsektors - in einer Boot-Loop fest. Zwischendrin hatte ich Arch schon aufgegeben und es mit Ubuntu 13.04 versucht - nur auch hier startete Grub nicht durch, sondern hing einfach. Schließlich gelang mir ein Systemstart nach einem vollständigen Überschreiben der ersten 50 MB der Festplatte durch /dev/random, und einer Neuinstallation von Arch vom allerersten Schritt an.

Anschließend installierte ich Grafiktreiber, X, Gnome, aktivierte Swap in der /etc/crypttab und startete neu.

Der Laptop startete, ich muss mein Passwort für die Festplatten-Entschlüsselung eingeben, der Bildschirm flackert, und der Rechner steht. Erste Vermutung: Grafiktreiber. Keinerlei Debug-Meldungen nirgendwo.

Nach einer weiteren Arch Neuinstallation, da ich irgendwann einfach nicht weiter wusste, kam ich irgendwann durch Zufall und Faulheit auf den Trichter, dass es nicht die Grafik war (denn die ging dann auf einmal), sondern ein fehlerhaftes Einbinden der swap-Partition in der /etc/crypttab. Stand - wie ich im Nachhinein erfuhr - auch sehr klein im Kleingedruckten. So halb meine Schuld also, dennoch sehr, sehr schlecht zu diagnostizieren, und mit enormem Zeitverlust verbunden. Dass vergesse ich auch so schnell nicht wieder.