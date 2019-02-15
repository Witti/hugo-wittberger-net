---
title: "Printserver inklusive Airprint mit dem RaspberryPi"
date: 2014-06-03
Description: ""
coverimage: "images/rpicupstut.jpg"
squareimage: "images/rpicupstut_square.jpg"
tags: ['rpi','airprint','hardware','apple']
categories: ['RaspberryPi','Hardware','Linux']
---

### Voraussetzungen
Folgende Dinge setze ich für die erfolgreiche Durchführung voraus:

* einen [RaspberryPi Model B](https://amzn.to/2S4Utw6) (Model A sollte genauso funktionieren)
* eine frische Installation von Raspbian ([Details gibt es hier](http://www.RaspberryPi.org/help/noobs-setup))
* funktionierende Netzwerkverbindung
* Maus/Tastatur/Bildschirm-Verbindung (SSH geht natürlich auch)
* einen mit CUPS kompatiblen Drucker ([Details sind hier zu finden](https://www.openprinting.org/printers))

### Software updates
Zu Beginn sollte dafür gesorgt sein, dass alle Software-Pakete auf dem RaspberryPi auf dem neusten Stand sind. Dies geht sehr einfach über
	
	sudo apt-get update -y
	sudo apt-get upgrade -y

*Info: Der Parameter -y bedeutet, dass alle Ja/Nein-Eingabeaufforderungen mit Ja beantwortet werden.*

### Software installieren
Damit wir mit dem RaspberryPi drucken können brauchen wir erstmal ein "paar" Software-Pakete. Dies erledigen wir in mehreren Schritten und beginnen mit der Software [CUPS](http://www.cups.org).
 
	sudo apt-get install cups

Nachdem Cups erfolgreich installiert wurde fahren wir mit 

	sudo apt-get install python-cups
	
fort. 

Der Avahi-Daemon sollte durch die Installation von Cups bereits installiert sein. Zur Sicherheit prüfen wir das und installieren diesen bei Bedarf nach:

	sudo apt-get install avahi-daemon
	
Damit das Editiieren der Config-Files leichter fällt holen wir uns noch schnell VIM:

	sudo apt-get install vim
	
Jetzt haben wir die benötigten Software-Komponenten auf unserem RaspberryPi installiert und können mit der Konfiguration fortfahren. 

### Konfiguration von CUPS

Nun muss die Konfigurations-Datei von CUPS noch an mehreren Stellen angepasst werden. 

Dies erfolgt mit Hilfe des Editors VIM:

	sudo vi /etc/cups/cupsd.conf

Da wie schon erwähnt an mehreren Stellen eine Anpassung vorzunehmen ist habe ich die gesamte Config-Datei [hier](https://gist.github.com/Witti/df82f7be9d81af7ba505) online gestellt:

	https://gist.github.com/Witti/df82f7be9d81af7ba505

**Zu den Änderungen:**

* Zeile 17+18: Mit der Veränderung des "Listen"-Parameters zu "Port" sorgen wir dafür, dass Cups auf allen verfügbaren Netzwerkverbindungen lauscht.
* Zeile 34: Sorgt dafür, dass alle eingehenden Verbindungen korrekt akzeptiert werden.
* Zeilen 37, 43, 51: Erlaubt den Zugriff über das lokalte Netzwerk

### Zugriff gewähren
Damit wir später Zugriff auf die Druckerverwaltung erhalten, müssen wir unseren User ("pi") noch zu der entsprechenden Gruppe hinzufügen. 
Dies erfolgt mit dem Befehl 

	sudo usermod -aG lpadmin pi

*Der User "pi" am Ende des Befehlt kann natürlich gegen einen Beliebigen vorhandenen User ersetzt werden. Bei der Standard-Raspbian-Installation ist pi jedoch der Account des Nutzers.*

### Dienste Starten
Jetzt da alles soweit konfiguriert ist starten wir die benötigten Dienste:

	sudo /etc/init.d/cups start
	sudo /etc/init.d/avahi-daemon start 

### IP-Adresse des PI besorgen
Falls nicht bereits über SSH gearbeitet wird kann man sich mit dem Kommando 

	ifconfig

die IP-Adresse des RaspberryPi besorgen. Diese wird für den nächsten Schritt (die Druckerkonfiguration) benötigt. 

### Drucker einrichten
Um einen Drucker hinzuzufügen rufen wir als erstes unsere Cups-Installation über den Browser eines beliebigen Rechners im Netzwerk auf.
Als Adresse verwenden wir hier die IP-Adresse + den Port 631. 

	zB: https://10.0.0.232:631/

Es sollte eine kurze Zertifikats-Warnung kommen die bestätigt werden muss. 

Nun sollte das Admin-Interface von Cups in voller Pracht im Browser erscheinen.

![Cups Admin Interface](images/cups_screenshot.png)

Unter dem Menüpunkt "Verwaltung" befindet sich die Verwaltung von Cups und der verfügbaren Drucker. 
Diese Verwaltung ist grundsätzlich komplett selbsterklärend. Mit einem Klick auf "Drucker hinzufügen" kann der angeschlossene USB-Drucker zu Cups hinzugefügt werden. Dort sollte unbedingt auf die Wahl des richtigen Modells geachtet werden damit der Drucker korrekt angesprochen werden kann und das beste Druckergebniss erreicht wird. 

Beim Klick auf Drucker-Hinzufügen fordert Cups uns auf Username und Passwort einzugeben. Hier nutzen wir unseren User "pi" den wir zuvor zur entsprechenden Gruppe hinzugefügt haben. 

![Cups Admin-Interface - Drucker hinzufügen](images/cups_add_printer.png)

Bei meinem Drucker - Samsung ML-2510 - funktionierte die Integration in Cups ohne Probleme.
Nachdem der Drucker zu Cups hinzugefügt wurde sollte dieser auch schon auf allen Geräten im Netzwerk verfügbar sein. Selbiges gilt für iOS-Devices. 

### Anmerkungen
* Bei älteren Versionen von Raspian besteht die Möglichkeit, dass der Avahi-Damon seperat konfiguriert werden muss. Ich habe für meine Umsetzung die Version 1.3.7 von NOOBS verwendet. 
* Damit unser neuer PrintServer auch ohne Netzwerkkabel funktioniert kann man einen USB-WLAN-Adapter an den RapsberryPi anschließen. Ich habe mit [diesem Gerät von Edimax](http://www.amazon.de/gp/product/B003MTTJOY/ref=as_li_ss_tl?ie=UTF8&camp=1638&creative=19454&creativeASIN=B003MTTJOY&linkCode=as2&tag=witstri-21) bisher sehr gute Erfahrungen gemacht. Die Konfiguration des WLAN-Adapters kann man per Google finden oder [diese hier](https://RaspberryPi4dummies.wordpress.com/2013/03/16/how-to-setup-a-usb-wlan-connection/) verwenden :-)

### Fragen und Anregungen
Für Fragen und Anregungen stehe ich gerne über die Kommentarfunktion zur Verfügung. 

*Headerbild: Platine (http://commons.wikimedia.org/wiki/File:RaspberryPi.jpg), CUPS-Logo (http://commons.wikimedia.org/wiki/File:RaspberryPi.jpg), RaspberryPi-Logo (http://www.RaspberryPi.org/)*
