---
title: The year of … the Printer.
author: penguin
type: post
date: 2013-07-01T09:32:30+00:00
url: /2013/07/01/the-year-of-the-printer/
categories:
  - Uncategorized
tags:
  - arch
  - desktop
  - linux
  - printing
  - rant

---
Ah, ich bin langsam - der Post schimmelt schon eine Weile hier vor sich hin, weil ich den Screenshot hochlade. Jetzt isses soweit. Es geht also weiter mit: Drucken. Denn irgendwann lief das System schließlich, und ich kam an den Punkt, an dem ich etwas drucken wollte. Gnome war installiert, Firefox auch, also theoretisch (kenne ich ja vom Mac, da verrichtet CUPS ja auch hervorragende Dienste) einfach die WebGui aufrufen, Drucker auswählen, fertig.

Zuallererst musste ich an Linus denken: root-Passwort-Eingabe für die Administration von CUPS nötig. Hm. Egal, eingegeben, dann "Add Printer" geklickt, und erst mal erfreut folgendes gesehen:

[<img loading="lazy" class="size-medium wp-image-19 aligncenter" alt="add_printer" src="http://sysadminia.files.wordpress.com/2013/07/add_printer.png?w=300" width="300" height="254" srcset="https://flypenguin.de/wp-content/uploads/2013/07/add_printer.png 785w, https://flypenguin.de/wp-content/uploads/2013/07/add_printer-300x255.png 300w, https://flypenguin.de/wp-content/uploads/2013/07/add_printer-768x652.png 768w" sizes="(max-width: 300px) 100vw, 300px" />][1]

Hm. Sieht erst mal gut aus, oder? Na, jedenfalls bis man sich die Frage stellt: Welcher der Drucker ist jetzt genau der hier im Büro? Nur Typenbezeichnungen, kein Standort, keine IP? Nicht optimal.

Der Einfachheit halber einfach manuell installieren. IP vom Kollegen erfragt, und los. Und stop. Drucker URL eingeben ... nur wie? Mit "http://"? Oder "lpd://"? Oder "ipp://"? Und hier dann mit oder ohne "/ipp" am Ende? Ich entschied mich für "ipp://" ohne "/ipp". Dann bitte "Make and Model" auswählen, Brother war nicht in der Liste, Ende.

Aber kein Problem, Brother gibt ja eigens Linux-Treiber raus. Nur nicht auf der "normalen" Brother-Downloads-Seite, die hat nur Mac und Windows-Treiber. Hm. Ich bin faul, also mal die Variante versucht, die früher mal ging: Zurück, HP Drucker ausgewählt, PCL6, Testseite gedruckt, klappt ... nicht. Na gut, wäre auch nicht schön gewesen, aber trotzdem.

Nochmal zurück, Drucker gelöscht. Dann Google bemüht, die "richtige normale" [Brother-Homepage für den Linux-Support][2] gefunden (wäre unter Support -> Linux gewesen, und nicht Support -> Downloads, aber ehrlich, wer schaut noch den Rest der Seite an wenn er das Bild unten sieht? Ich jedenfalls nicht).

[<img loading="lazy" class="aligncenter size-full wp-image-15" alt="brother-homepage" src="http://sysadminia.files.wordpress.com/2013/06/brother-homepage1.png" width="696" height="183" srcset="https://flypenguin.de/wp-content/uploads/2013/06/brother-homepage1.png 745w, https://flypenguin.de/wp-content/uploads/2013/06/brother-homepage1-300x79.png 300w" sizes="(max-width: 696px) 100vw, 696px" />][3]Dort gibts für das von mir gesuchte Modell 5 Treiber zum herunterladen, und zwar folgende:

  * LPR driver (jeweils als rpm + deb)
  * cupswrapper driver (jeweils als rpm + deb)
  * ppd file

Aha. Ich entschied mich für PPD, ich bin ja CUPS, ne, und nochmal von vorn: Drucker hinzufügen, manuell, URL diesmal mit "ipp://.../ipp" (also mit "/ipp" am Ende), und siehe da - jetzt kamen auch automatisch Brother-Geräte in der Liste (WTF?), wenn auch nicht das Modell hier im Büro. Daher runtergeladenes PPD eingebunden, bestätigt, Testseite gedruckt, fertig.

Nicht so ganz optimal.

 [1]: http://sysadminia.files.wordpress.com/2013/07/add_printer.png
 [2]: http://welcome.solutions.brother.com/bsc/public_s/id/linux/en/index.html
 [3]: http://sysadminia.files.wordpress.com/2013/06/brother-homepage1.png