---
title: "Jekyll Auf Uberspace Mit Git"
date: 2019-01-25T21:57:27+01:00
Description: ""
tags: ["git","staticsite","uberspace"]
categories: ['Uberspace','HowTo']
---

Diese kleine Anleitung beschreibt die Verwendung von Jekyll mit Hilfe von Git auf einem uberspace.de Account. 

### Ruby und Gems vorbereiten (uberspace)

Falls du auf deinem uberspace.de Account noch kein Ruby eingerichtet hast solltest du dies zuerst mal erledigen. Jekyll benötigt eine Ruby-Version von 1.9 oder höher. 

### Hier nun Schritt für Schritt die Anleitung wie dies erfolgt. (Für diese Schritte setze ich einen "jungfräulichen" uberspace-Account voraus)

	[helga@helium ~]$ cat <<'__EOF__' >> ~/.bash_profile
	export PATH=/package/host/localhost/ruby-2.0.0/bin:$PATH
	export PATH=$HOME/.gem/ruby/2.0.0/bin:$PATH
	__EOF__
	
	[helga@helium ~]$ . ~/.bash_profile
	
[Quelle](https://uberspace.de/dokuwiki/development:ruby)

Damit ist auch schon Ruby auf eine für Jekyll passende Version gebracht. Nun müssen wir noch die Installation von eigenen einrichten. Dies erfolgt mit einer Zeile Shell-Befehl:

	[helga@helium ~]$ echo "gem: --user-install --no-rdoc --no-ri" > ~/.gemrc
	
[Quelle](https://uberspace.de/dokuwiki/development:ruby)

Damit steht der Installation von Jekyll nichts mehr im Wege und wir erledigen dies mit 

	[helga@helium ~] gem install jekyll
	
Nach einer kurzen Wartezeit ist nun auch Jekyll für die spätere Verwendung installiert. 

### Jekyll auf dem lokalen System installieren

Hier wird es etwas schwierig für mich eine Anleitung anzubieten da es einfach zu viele unterschiedliche Systeme gibt auf denen Ruby und Jekyll laufen kann. Um dies trotzdem in einer vernünftigen Form anzubieten behelfe ich mich der alten Internet-Technik und zwar der Hyperlinks :-D

Hier nun ein paar Links zu entsprechenden Anleitungen:

* [Jekyll auf Linux, Unix oder OSX](http://jekyllrb.com/docs/installation/)
* [Jekyll auf Windows - nicht getestet, weil kein Windows-System vorhanden](http://bryanwweber.com/2013/07/installing-jekyll-on-windows/)


### Git auf dem lokalen System installieren
Falls dein lokales System noch über keine Git-Installation verfügt solltest du dies noch schnell nachholen. Wie auch bereits bei dem Punkt darüber behelfe ich hier mir mit einer Verlinkung :-)

* [Anleitungen und Downloads von Git für die gängigen Plattformen](http://git-scm.com)

### Jekyll auf dem lokalen System vorbereiten

Ist nun Jekyll und Git auf dem eigenen System installiert können wir mit der Erstellung unseres ersten Jekyll-Blogs fortfahren. Hier die wichtigsten Basics um ein Jekyll-Blog am eigenen System zu erstellen:

Mit folgendem Shell-Befehl wechseln wir in das Verzeichnis in das unser neues Jekyll-Blog abgelegt werden soll: 

`[user@lokal ~] cd /dort/wo/das/blog/abgelegt/werden/soll `

Jetzt erstellen wir ein Jekyll-Blog (für blogname setzt bitte den gewünschten Namen des Blogs ein): 

`[user@lokal ~] jekyll new blogname`

Mit Hilfe von `cd` wechseln wir jetzt in den neu generierten Ordner (hier bitte den oben verwendeten Blognamen einsetzen.

`[user@lokal ~] cd blogname`

In diesem Ordner finden sich nun einige Dateien und Ordner die Jekyll für uns angelegt hat. 

* _config.yml
* _layouts
* _posts
* css
* index.html

*Welche Aufgaben diese Dateien und Ordner haben entnimmst du bitte der [Jekyll-Dokumentation](http://jekyllrb.com/docs/home/).*

Nun kommen wir zurück zu Git. Um unser Jekyll-Blog später mit Hilfe von Git versionieren und übertragen zu können verwandeln wir unseren Jekyll-Blog-Ordner in ein lokales Git-Repository und machen unseren ersten Commit.

	[user@lokal ~] git init
	[user@lokal ~] git add .
	[user@lokal ~] git commit -m "erster Commit"
	
*Falls du mit Git noch nicht ganz sattelfest bist schau bitte [hier](http://try.github.io/levels/1/challenges/1) vorbei. Dort kannst du die Grundlagen von Git innerhalb weniger Minuten erlernen.*

Mit den letzten 3 Kommandos ist die Arbeit auf unserem lokalen System nahezu erledigt. Jetzt müssen wir aber erstmal auf unserem uberspace-Account noch ein paar Dinge erledigen. 

### Git-Repository auf dem uberspace-Account vorbereiten

Für unser neues Jekyll-Blog brauchen wir jetzt auf dem uberspace-Account eine Gegenstelle sprich ein Git-Repository. Dazu wechseln wir mit Hilfe von 

`[helga@helium ~] cd ~`

in unser Home-Verzeichnis und legen dort durch

`[helga@helium ~] mkdir repos`

ein neues Verzeichnis an in dem wir all unsere Git-Repositories aufgewahren. Jetzt brauchen wir noch ein Verzeichnis, dass gleichzeitig als Git-Repository dienen soll. Dies erledigen wir mit Hilfe folgender Shell-Befehle:

	[helga@helium ~] mkdir blogname.git
	[helga@helium ~] cd blogname.git
	[helga@helium ~] git init --bare
	
Für den Begriff blogname bitte wieder den vorher verwendeten Namen einsetzen. Durch das gerade abgesetzte Kommando `git init --bare` haben wir ein den leeren Ordner in ein Git-Repository verwandelt und darin befindet sich nun ein Ordner mit dem Namen **hooks**. Diesen Ordner öffnen wir nun mit einem einfachen 

`[helga@helium ~] cd hooks`

Im `hooks`-Ordner angelangt erstellen wir dort eine Datei mit dem Titel `post-receive` und editieren diese zugleich. Die erfolgt mit folgenden Shell-Befehlen:

	[helga@helium ~] touch post-receive
	[helga@helium ~] nano post-receive

Nun haben wir den Nano-Editor vor und und fügen dort folgende Zeilen per Copy/Pase ein:

	#!/bin/bash -l
	GIT_REPO=$HOME/repos/blogname.git
	TMP_GIT_CLONE=$HOME/tmp/git/blogname
	PUBLIC_WWW=/var/www/virtual/$USER/html

	git clone $GIT_REPO $TMP_GIT_CLONE
	jekyll build --source $TMP_GIT_CLONE --destination $PUBLIC_WWW
	rm -Rf $TMP_GIT_CLONE
	exit
	
Bitte ersetze wieder blogname mit deinem vorher verwendeten Begriff. 
Mit dem Tastenkürzel `STRG + O` und einem anschließenden beherzten Druck auf die Entertaste speichern wir unser neu erstelltest Script ab und laden wieder auf der Kommando-Zeile. 

Jetzt müssen wir das neu erstellte Script nur mehr ausführbar machen, dies erledigen wir mit Hilfe von 

`chmod +x post-receive` 

Somit sind nun alle Schritte auf der Seite unseres uberspace.de Accounts erledigt und wir kehren wieder zurück zu unserem Arbeitsrechner. 

### uberspace-Account als Remote eintragen und pushen

Damit unsere Blog-Einträge und Änderungen auch auf dem Server landen müssen wir unsere frisch erstellte Git-Gegenstelle noch bei unserem lokalen Git-Repository als "Remote" hinterlegen. 

Dazu wechseln wir in das Verzeichnis unseres zuvor lokal erstellten Jekyll-Blogs und geben dort folgendes in die Kommando-Zeile ein:

`git remote add uberspace helga@helium.uberspace.de:repos/blogname.git`

**Bitte ersetze hier hela@helium.uberspace.de mit deinen korrekten uberspace.de Zugangsdaten und blogname wieder durch den zuvor verwendeten Begriff**

Nun ist der Zeitpunkt gekommen und wir starten die Veröffentlichung unseres Jekyll-Blogs mit Hilfe von Git. Dies erfolgt mit dem Kommando 

`git push uberspace master`

Mit Hilfe dieses Kommandos werden alle Änderungen am Jekyll-Blog zum uberspace-Account übertragen und dort durch das post-receive-Script verarbeitet. Ist diese Verarbeitung abgeschlossen sollte dein Jekyll-Blog online sein. 

### Fertig

Ich hoffe du hast alles verstanden und dein Jekyll-Blog ist nun online. Hast du noch Fragen dann poste Sie einfach als Kommentar und ich werde versuchen dir zu helfen. 

Wenn du noch tiefer in die Thematik von Jekyll eintauchen möchtest hier ein paar interessante Links:

*  [Jekyll-Webseite](http://jekyllrb.com)
*  [Plugins für Jekyll](http://jekyllrb.com/docs/plugins/)
*  [Ausgesprochen guter Markdown-Editor für OSX](http://www.markdownpro.com)
*  [E-Book zum Thema Jekyll](https://leanpub.com/jekyll)

_Bildquelle: [Death to the Stock Photo](http://join.deathtothestockphoto.com)_
