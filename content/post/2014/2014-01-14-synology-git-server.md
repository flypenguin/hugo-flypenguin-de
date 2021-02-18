---
title: Synology Git Server
author: penguin
type: post
date: 2014-01-14T21:31:36+00:00
url: /2014/01/14/synology-git-server/
categories:
  - Uncategorized
tags:
  - helpful
  - nas
  - synology

---
Ich besitze ab neulich eine wundervolle Synology DS414. Schönes Teil.

Und da ich aktuell ein wenig programmiere zu Hause wollte ich mir hier ein Git repository server einrichten. Das ist nicht ganz trivial, und wer sich github- oder gitlab-ähnlichen Komfort erhofft ... ah, nö. Es ist commandline only, und davon viel. Aber zum Punkt - der schnellstmöglichen Einrichtung eines Git-Repo-Servers auf einer Diskstation.

  * Der SSH-Zugriff unter `Systemsteuerung > Terminal` muss aktiviert sein.
  * Es müssen Benutzer angelegt sein, und diese müssen ein Home-Verzeichnis haben (für die ssh-Schlüssel, ohne die es keinen Spaß macht). Das home-Verzeichnis aktiviert man in der `Systemsteuerung > Benutzer`, dann oben ein Knopf `Benutzerbasis` (der 2. von rechts bei mir)
  * Wenn die Benutzer vorhanden sind legt man eine Freigabe an, das macht die Sache einfacher. Ich nutze `/volume1/gitrepos`.
  * Den eigenen ssh-Schlüssel ins home-Verzeichnis kopieren. Das sollte unter `/volume1/homes/<Benutzer>` erreichbar sein, das Volume hat man vorhin in Schritt (1) ausgewählt. (natürlich muss `.ssh/` 0600 und <nutzer>.<Gruppe> sein, und `.ssh/authorized_keys` 0700 und <nutzer>.<gruppe>, aber das muss ich ja nicht extra erwähnen, oder?)
  * Jetzt den Git-Service aktivieren, dem gewünschten Benutzer den Zugriff erlauben.
  * Ein repository anlegen ist leider Handarbeit:
      * Anmelden als root, dann nach `/volume1/gitrepos` wechseln, ein Verzeichnis anlegen ...
      * ... `git init --bare` ausführen, ... und
      * und jetzt entweder alles word-writable machen (bäh), oder dem entsprechenden Benutzer mittels chown zuweisen (yup).
      * Beispiel: `/volume1/gitrepos/my_repo`
  * Schließlich auf dem Arbeitsrechner noch git das neue Repo übergeben:
    `git remote add origin <user>@<diskstation>:/volume1/gitrepos/my_repo`
  * ... und das wars.

Ganz einfach, oder? Naja.

Quellen: [Synology Wiki][1], [Synology Forum][2]

 [1]: http://is.gd/NyNZSK
 [2]: http://is.gd/jKhpg7