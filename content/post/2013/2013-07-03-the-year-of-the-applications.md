---
title: The year of the … Applications.
author: penguin
type: post
date: 2013-07-03T18:21:46+00:00
url: /2013/07/03/the-year-of-the-applications/
categories:
  - Uncategorized
tags:
  - desktop
  - rant

---
Es gibt ein paar Dinge ohne die ich nicht mehr so richtig leben möchte: Text expander, clipboard history, intuitiver Editor (ViM und emacs zählen nicht), und ein paar andere hätte ich gerne. Und hier ist des Linux größtes Plus des Linux größter Fluch: Die Flexibilität. Ein paar recht Gnome-lastige Eindrücke:

**Text expander**: Hier tut [AutoKey][1] einen guten Job. Verwendet Python als Skriptsprache für "fancy stuff", schon ziemlich gut. Leider führt ein fehlerhaftes Skript zu einem Einfrieren der Applikation, weniger praktisch. Außerdem werden die Tastaturkürzel nicht nach der Zuweisung aktiv - sondern erst nachdem man in der tabellarischen Übersicht auf die "Abbr" (Abbreviation) Spalte der richtigen Zeile klickt, denn dann erst "erscheint" der Kürzel dort, und dann erst ist er aktiv. Muss wohl ein Bug sein. Dennoch - klar der funktionale Favorit der Liste hier.

**Clipboard manager**: Gibts. Aber nicht von Gnome, nur "normales" Gtk+ Programm. Nimmt aber bei weitem nicht alle Tastaturkombis an (ich bin z.B. CTRL-Ö gewohnt, keine Chance). Bedienung: naja. Führt dazu dass ich ihn nicht nutze, und daher recht angepisst bin.

**Chat** app: Gnome hat Empathy. Dies kann jedoch kein OTR (off-the-record messaging, Verschlüsselung für Chats). Nicht weiter schlimm, gäbe es nicht [ein Ticket aus 2008][2] und gestartete Open-Source-[Bemühungen][3]. Die FAQ dazu trocken: "[Man ist der Meinung][4]" dass Lösungen wie OTR nicht wirklich zielführend sind, da das Protokoll Verschlüsselung inhärent unterstützen sollte. Das wolle man dann unterstützen (also XMPP mit Jingle, nichts anderes). Außerdem: Da Empathy auf [Telepathy][5] beruht müsste zuerst die Telepathy lib erweitert werden, "wir sind nicht zustädig" kommt da rüber. Da ich das aus Prinzip möchte nutze ich Pidgin, dessen Integration in Gnome ... "ok" ist. Dafür wesentlich schöner zu bedienen mit einer eigenen Menüleiste, und sinnvollen Menüs.

**Editor**. Wir alle kennen Notepad++, ViM und emacs. Meine emacs-Zeiten sind lange vorbei (schade irgendwie, aber so isses halt), daher finde ich die beiden letzteren einfach befremdlich - zu viel mehr als ":wq" in ViM oder CTRL-X CTRL-C in emacs reichts nicht mehr. Dafür ist unter Windows Notepad++ sehr gut für produktives Arbeiten geeignet. Für Gnome: Gedit. Und das ist vergleichbar mit Notepad. Ohne "++". Eine vollkommen sinnfreie app. Glücklicherweise gibts Sublime Text für Linux (danke, danke, danke!).

**Allgemein** sind die Ecken sehr, sehr rund:

  * Notifications. Die Gnome-Notifications sind zwar nett, aber das kleine "x" oben rechts reagiert bei mir zumindest immer erst auf den zweiten Klick.
  * Fonts. Warum hat Freetype nicht von vorneherein Ubuntu-Patches installiert? Das ist ein kaum zu beschreibender Unterschied. Nicht legal?
  * Keyboard-Shortcuts: Manchmal konfigurierbar, manchmal nicht, manche per Textdatei, manche GUI. Manchmal kann man sie zuweisen, funktionieren aber nicht. Manchmal ist eine Vorauswahl in Modifier-Keys oder insgesamt in Shortcuts getroffen.
  * Wenn KDE nun mal die bessere Applikation für etwas hat, dann sieht das aus wie Java Swing unter Windows: Fremd. Genauso umgekehrt.
  * Bookmarks in Nautilus kann man nur durch CTRL-D oder einen Klick auf das "Optionen"-Zahnrad (da?!?) setzen. Nicht per drag-and-drop, und nicht über das rechts-Klick-Menü, was beides ca. 100x intuitiver wäre.
  * Die Zuweisung von Dateiendungen zu Programmen funktioniert irgendwie ... nicht.
  * Den Hauptbildschirm in den Display-Einstellungen festzulegen ist _sehr_ unintuitiv (man ziehe den winzigen schwarzen Balken von einem Monitor auf den anderen - wenn man das weiß total einfach, ansonsten eher unintuitiv)

Und demnächst muss ich einfach mal einen positiven Post schreiben, schließlich arbeite ich mit dem System, und das auch noch gerne ...

 [1]: http://code.google.com/p/autokey/
 [2]: bugs.freedesktop.org/show_bug.cgi?id=16891
 [3]: https://bugs.launchpad.net/libtelepathy/+bug/296867
 [4]: https://wiki.gnome.org/Empathy/FAQ#Will_Empathy_have_OTR_.28.22Off_The_Record.22.29_support.3F
 [5]: http://telepathy.freedesktop.org/wiki/