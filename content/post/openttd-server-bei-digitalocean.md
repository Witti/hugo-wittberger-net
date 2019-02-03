---
title: "Openttd Server Bei Digitalocean"
date: 2019-01-25T21:56:46+01:00
Description: ""
tags: ["games","digitalocean","howto"]
categories: ['HowTo', 'DigitalOcean']

---

### Starten des Droplets
Für unseren OpenTTD-Server benötigen wir keinen sonderlich leistungsstarken Server. Ein 512MB-Ubuntu-13.10-Droplet reicht für den Anfang vollkommen aus. Dieses starten wir über das Backend von Digital-Ocean.
Ist das Droplet erstmal gestartet, suchen wir uns die zugewiesene IP-Adresse raus und loggen uns mit
	ssh root@111.111.111.111
ein.
_(111.111.111.111 bitte durch die korrekte IP des droplets ersetzen ;-D)_

### Droplet einrichten
Bevor wir mit der Einrichtung des OpenTTD-Servers beginnen können, müssen wir vorher ein paar Schritte durchführen.

Erstmal bringen wir die Software des Servers auf den neusten Stand. Dies erfolgt mit Hilfe von apt-get:
	apt-get update
	apt-get upgrade -y

Apt holt sich nun die neusten Versionen der installierten Software-Pakete und installiert diese für uns. Dank SSD und guter Verbindung bei [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5) sollte dies innerhalb weniger Minuten erledigt sein.

Jetzt holen wir uns noch 2 Software-Pakete die wir später benötigen:
	apt-get install unzip screen -y

Auch dies sollte nur eine Angelegenheit von Sekunden sein.

Damit unser OpenTTD-Server später nicht mit root-Rechten unterwegs ist, legen wir uns noch einen User an:
	adduser openttd
_Anmerkung: Der User muss nicht zwingend "openttd" heißen._

### Installation des OpenTTD-Servers
Jetzt da unser Droplet fertig eingerichtet ist, besorgen wir uns die neuste Version von OpenTTD. Dies erfolgt über die [Webseite von OpenTTD](http://www.openttd.org/en/download-stable).

Mit Hilfe von `wget` übertragen wir das OpenTTD-Paket auf unser Droplet:
	wget http://binaries.openttd.org/releases/1.3.3/openttd-1.3.3-linux-ubuntu-raring-amd64.deb

_Wichtige Anmerkung: Unbedingt die Architektur eures Droplets beachten (32 oder 64bit)_

Sobal das Paket auf unser Droplet geladen ist, installieren wir dies mit Hilfe von:
	dpkg -i openttd-1.3.3-linux-ubuntu-raring-amd64.deb
Dies wird jedoch wegen fehlender Abhängigkeiten nicht funktionieren. Kein Problem  - denn wir lösen dies umgehend mit Hilfe von:
	apt-get -f install -y
Und schon ist OpenTTD installiert.

### Einrichten des OpenTTD-Servers
Um mit der Einrichtung der Server-Software für OpenTTD fortzufahren, loggen wir uns aus (`exit`) und erneut mit unserem openttd-User ein:
	ssh openttd@111.111.111.111
_Anmerkung: Bitte hier den User ("openttd") abändern falls ein anderer Username verwendet wurde und auch wieder die korrekte IP verwenden ;-)_

Nun starten wir unseren OpenTTD-Server indem wir
	openttd -D
eingeben.

Aus irgendeinem unerfindlichen Grund meckert OpenTTD nun, dass kein Grafik-Paket installiert ist. Grafik-Paket .... auf einer Konsole für einen Server? O_O
Nun gut, tun wir unserer kleinen "Transport-Diva" den gefallen und installieren wir das Grafik-Paket.

### Grafik-Paket installieren
Als erstes besorgen wir uns über die Webseite die aktuelle Version des "OpenGFX" Grafik-Pakets. Die Webseite findet ihr [hier](http://dev.openttdcoop.org/projects/opengfx).

Wieder mit der Hilfe von wget laden wir das Grafik-Paket auf unser Droplet:
	wget http://bundles.openttdcoop.org/opengfx/releases/LATEST/opengfx-0.4.7.zip

_Anmerkung: Es ist nicht zwingend notwendig dies mit wget zu erledigen, es ist auch möglich das Grafik-Paket mit Hilfe von SCP auf den Server zu kopieren. (Passende Software ist zB: [Cyberduck für Mac](http://cyberduck.io) oder [WinSCP für Windows](http://winscp.net/eng/docs/lang:de) )._

Da wir ein ZIP-File bekommen müssen wir dies zuerst auspacken:
	unzip opengfx-0.4.7.zip

Sobald es entzippt ist, kopieren wir die Dateien in den Arbeits-Ordner von OpenTTD:
	mv ~/opengfx-0.4.7/* ~/.openttd/baseset/

Danach räumen wir noch mit
	rm -rvf opengfx-0.4.7*
hinter uns auf.

### OpenTTD-Server starten (jetzt aber wirklich)
Nun starten wir unsern Server erneut:
	openttd -D

Nun sollte der Server starten und anhand der Voreinstellungen eine neue Karte generieren.
Mit Hilfe des Spiel-Clients ist es nun möglich sich mit dem Server zu verbinden und mit Freunden und Verwandten OpenTTD zu genießen.

Eine Kleinigkeit gibt es aber noch. Falls wir unsere Konsole nun schließen ist der OpenTTD-Server auch weg.
Um den OpenTTD-Server dauerhaft laufen zu lassen verwenden wir das Tool "screen". Uns so funktioniert es:
	screen openttd -D

Und schon läuft unser Server. Mit CTRL + A + D können wir die Konsole nun verlassen.

Mit dem Kommando
	screen -r
können wir uns wieder Zugriff auf die Admin-Konsole von OpenTTD verschaffen.

### Weitere Einrichtung und Anpassungen
Um unseren Server anzupassen und zu gestalten bietet OpenTTD eine Vielfalt an Optionen innerhalb der Konfigurations-Datei.
Diese ist im Heimat-Ordner unseres openttd-Users unter .openttd zu finden.

Alle möglichen Optionen und deren Bedeutung findet ihr in der OpenTTD-Wiki unter [http://wiki.openttd.org/Openttd.cfg](http://wiki.openttd.org/Openttd.cfg).

### Unterstützung
Sollte das Tutorial hilfreich gewesen sein und die Entscheidung für einen [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5)-Account gefallen sein dann wäre es toll wenn ihr dafür meine Links zu [DigitalOcean](https://www.digitalocean.com/?refcode=2fa486f567f5) verwendet.

_Bildquelle: [OpenTTD - eigener Screenshot](http://www.openttd.org)_
