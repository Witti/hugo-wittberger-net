---
title: "Starbound Server Bei Digitalocean"
date: 2019-01-25T21:42:09+01:00
draft: false
---

Das vor kurzem erschienene Spiel Starbound befindet sich zwar noch in Entwicklung aber trotzdem ist es bereits möglich, einen eigenen Multiplayer-Server aufzusetzen. Wir wollen dies anhand dieser kleinen Anleitung bei [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5)  praktisch umsetzen. 

### Droplet starten
Zu Beginn benötigen wir erstmal einen Server bzw. ein Droplet bei [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5) . Für einen kleinen Server für eine überschaubare Anzahl von Spielern sollte ein 512 MB RAM Ubuntu 13.10 x64 Droplet ausreichen. 
Dieses Droplet starten wir über die Adminoberfläche von [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5) . 

### Ein wenig Swap
Ist unser Droplet fertig gestartet besorgen wir uns die IP-Adresse aus der Admin-Oberfläche und loggen uns als Root-User ein. 
	ssh root@111.111.111.111
_Anmerkung: Die 111.111.... bitte durch die korrekte IP-Adresse ersetzen_

Um sicher zu gehen, dass unserem Droplet nicht der Ram-Speicher zu knapp wird fügen wir etwas Swap-Speicher hinzu. 
Dies erfolgt mit den folgenden Befehlen über die SSH-Konsole:
	dd if=/dev/zero of=/swapfile bs=2048 count=512k
	mkswap /swapfile
	swapon /swapfile
	echo 0 > /proc/sys/vm/swappiness
	chown root:root /swapfile 
	chmod 0600 /swapfile
Um sicher zu gehen, dass bei jedem Systemstart auch das Swap-File geladen wird öffnen wir mit Hilfe eines Editors das fstab-File und legen eine Zeile für das Swapfile an:
	nano /etc/fstab
Am Ende der Datei fügen wir folgendes ein:
	 /swapfile       none    swap    sw      0       0 
Damit ist unser Server nun mit etwas Swap-Speicher ausgestattet.

_Quelle: https://www.digitalocean.com/community/articles/how-to-add-swap-on-ubuntu-12-04_

### Nutzer anlegen
Damit unser geplanter Server nicht mit Root-Rechten läuft brauchen wir noch einen User. Der Nutzername ist frei wählbar - als Beispiel verwende ich hier den Usernamen _starbound_.
	adduser starbound
Das Kommando fragt uns nach ein paar Daten die wir so korrekt wie möglich ausfüllen und zum Abschluss bestätigen. 

### Pakete installieren
Damit der Steam-Client, welchen wir für den Download der Server-Software  auf unserem System auch lauffähig ist, benötigen wir noch ein 32-Bit-Paket und für den späteren Gebrauch screen. Dieses wird mit Hilfe folgender Eingabe installiert:
	apt-get install -y lib32gcc1 screen

### Steam installieren
Mit dem letzten Schritt sind nun alle Vorarbeiten erledigt und wir können dazu übergehen uns den Steam-Client zu besorgen. 

Damit wir gleich mit dem korrekten Nutzer unterwegs sind, loggen wir uns erneut über SSH am Server ein. Diesmal aber nicht als Root-User sondern mit dem vorher erstellten User.
	ssh starbound@111.111.111.111
_Anmerkung: Bitte auch hier wieder die korrekte IP-Adresse einsetzen und falls ein anderer Username als starbound angelegt wurde bitte auch diesen korrekt einsetzen._

Als Alternative zum erneuten Einloggen könnte man auch das Kommando 
	su - starbound
verwenden, aber um spätere Probleme zu vermeiden gehe ich hier auf Nummer sicher und logge mich erneut ein. 

Sind wir wieder auf unserem Server eingeloggt, besorgen wir uns den Steam Client. Dies erledigen wir mit folgenden Kommandos:
	wget http://media.steampowered.com/client/steamcmd_linux.tar.gz
	tar xvfz steamcmd_linux.tar.gz
	rm steamcmd_linux.tar.gz
Das letzte Kommando dient nur dazu um hinter uns aufzuräumen und muss nicht zwingend ausgeführt werden. 

### Einloggen bei Steam
Damit wir Zugriff auf unsere Spiele haben, ist es notwendig, uns mit dem Steam-Client einzuloggen. 
*Wichtig: Unbedingt auf dem Client-Rechner den Steam-Client schließen, da es sonst zu Problemen kommen kann.*

Mit folgendem Kommando starten wir den Steam-Client:
	./steamcmd.sh

Jetzt sollte sich unsere Kommandozeile etwas verändert haben und uns mit dem Wort "Steam>" begrüßen. 
Nun ist es Zeit sich einzuloggen. Dies erfolgt per
	login --steamuser--
_Anmerkung: --steamuser-- durch euren eigenen Steam-Benutzernamen ersetzen (ohne --).

Sogleich werden wir nach unserem Passwort gefragt, welches wir auch eingeben. Danach erfolgt noch eine Abfrage des Steam-Guard-Codes. Dieser Code wird euch an die bei Steam hinterlegte E-Mail-Adresse zugesandt. 

### Starbound installieren
Nun da wir bei Steam eingeloggt sind, können wir Starbound installiern. Dies erfolgt durch die Eingabe von
	app_update 211820
_Anmerkung: 211820 stellt die ID der Applikation innerhalb von Steam dar._

Nun erfolgt der Download von Starbound auf euren Server. Dies kann ein Weilchen dauern.

Ist der Download abgeschlossen beenden wir den Steam-Client mit Hilfe der Tastenkombination ***CTRL+C***.

### Starbound starten
Es ist endlich soweit - wir können unseren Starbound-Server in Betrieb nehmen. Dazu wechseln wir in das Verzeichnis der Linux-Binaries:
	cd ~/Steam/SteamApps/common/Starbound/linux64/

Dort angekommen stellen wir sicher, dass unsere Server-Software auch ausführbar ist:
	chmod +x starbound_server

Und starten anschließend den Server:
	screen ./starbound_server

### Weitere Infos
Die Config-Datei für Starbound kann mit folgendem Kommando geöffnet und editiert werden:
	nano ~/Steam/SteamApps/common/Starbound/assets/default_configuration.config

###Unterstützung
Sollte das Tutorial hilfreich gewesen sein und die Entscheidung für einen [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5)-Account gefallen sein, dann wäre es toll wenn ihr dafür meine Links zu [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5) verwendet. 

_Bildquelle: [Starbound Webseite](http://playstarbound.com/media/)_