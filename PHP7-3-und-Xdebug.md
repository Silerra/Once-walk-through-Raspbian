# PHP 7.3 und Xdebug
Oft ist es so, dass das eigentliche Raspbian-Repository fast nur Pakete anbietet, die schon ein paar Jahre zurück liegen. (Im Schnitt knapp 2 bis 3 Jahre!) Jetzt aktuell findet man auf diesem Repository die Version 7.0+49. Was eigentlich noch nicht mal so schlecht ist. Ich hatte selber Anfang 2019 nur die PHP-Version 5.6 vorgefunden und wollte Xdebug einrichten. Es war irgendwie nicht miteinander kompatibel und ich bekam es einfach nicht zum Laufen. Also schaute ich nach Lösungen, welches ich auch gefunden habe.

Bei diesem Fall ist es sehr einfach. Da du hier nur auf Buster-Zweig zu zugreifen brauchst, ist das ganze auch schnell erledigt. Bevor du zum nächsten Abschnitt gehst, solltest du den Kapitel [Buster als weitere Quelle für Raspbian einrichten](Paketquellen#buster-als-weitere-quelle-für-raspbian-einrichten) abgearbeitet haben.


## PHP 7.3 auf Raspbian installieren
Nachdem der Release Buster für Raspbian in Quellenverzeichnis verfügbar ist, kannst du jetzt auf aktuellere Inhalte zugreifen wie z.B. PHP Version 7.3. Verfügbare Versionen kannst du mit dem Befehl
```bash
apt-cache madison php
```
anschauen. Die Ausgabe wird dann so ähnlichen aussehen wie:
```bash
$ apt-cache madison php
       php |   2:7.3+69 | http://mirrordirector.raspbian.org/raspbian buster/main armhf Packages
       php |   1:7.0+49 | http://mirrordirector.raspbian.org/raspbian stretch/main armhf Packages
$ _
```
An diesem Beispiel kannst du entnehmen, dass auf Raspbian Strech Repo die PHP-Version 7.0+49 verfügbar ist und auf Buster Repo die 7.3+69. Mit dem Befehl:
```bash
sudo apt install -t buster php
```
lässt sich jetzt ganz einfach die aktuellere Version installieren. Aber sei gewarnt, dass alle davon abhängige Pakete ebenfalls mit Befehl 'sudo apt install -t buster' installiert werden müssen. Ansonsten werden die weitere Module mit der PHP-Version 7.3 nicht zusammen arbeiten können.


## Xdebug für PHP 7.3 installieren und einrichten
### Installation Xdebug
Nun, da du jetzt die PHP-Version 7.3 installiert hast, so möchte du dann auch eventuell für diese Version auch die entsprechende Debugger einrichten. Aber zuerst schauen wir die Abhängigkeiten mit folgenden Befehl an:
```bash
aptitude show php-xdebug
```
Da werden einige Informationen angegeben, wie z.B. ob es bereits installiert wurde, ob es automatisch geschah und viele andere Sachen. Uns ist jetzt aber nur die Abhängigkeit interessant und bei dir steht wahrscheinlich sowas wie:
```bash
Hängt ab von: ucf, php-common (>= 1:7.0+33~), phpapi-20151012, libc6 (>= 2.4)
```
In diesem Beispiel würde jetzt mit normalen Befehl 'sudo apt install php-xdebug' php-common 7.0 und alle davon ausgehende Abhängigkeiten installieren. Das würde also bedeuten, dass die Pakete von PHP der Version 7.0 installiert werden. Somit hättest du PHP-Versionen 7.0 und 7.3 auf Raspbian installiert. Xdebug wird dann nur auf 7.0 laufen können. Zudem unterstützt die Xdebug-Version 2.5 nicht die aktuelle PHP-Version 7.3 und somit wäre dir auch nicht möglich einfach mit symbolischen Link auch auf PHP 7.3 verfügbar zu machen. Siehe dazu https://xdebug.org/docs/install#versions unter PHP Version Support.

Nun, wir wollen aber, dass Xdebug unter 7.3 läuft, also gebst du den Befehl:
```bash
sudo apt install -t buster php-xdebug
```

### Xdebug einrichten
Nach der Installation von Xdebug ist Diese unter PHP noch nicht lauffähig und muss vorher noch aktiviert werden. (So wie die anderen Module von PHP.) Mit folgenden Befehl:
```bash
file='/etc/php/7.3/mods-available/xdebug.ini' && cat $file
```
dürfte folgende Ausgabe ergeben:
```bash
zend_extension=xdebug.so
```
Hier siehst du, dass Xdebug jetzt verfügbar ist. Aber bevor du diesen Mod aktivierst, wäre vielleicht ein paar kleine Modifikationen nicht schlecht, damit du in deinem IDE wie z.B. unter VSCode, Eclipse oder so, direkt nutzen kannst.
```bash
echo "Folgende Zeilen in Datei '$file' hinzufügen:" && echo '
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
' | sudo tee -a $file
```
mit folgenden Befehl siehst du ob das geklappt hat:
```bash
cat $file && unset file
```
und so sollte die letzte Ausgabe aussehen:
```bash
zend_extension=xdebug.so


[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```
Jetzt brauchst du nur noch den Mod zu aktivieren:
```bash
phpenmod xdebug
```
Mit
```bash
php -v
```
kannst du sehen, ob Xdebug erfolgreich aktiviert wurde.

Und das wars, jetzt kannst du Xdebug in deinem IDE direkt nutzen. Der Standardport ist 9000. Weitere Informationen kannst du leicht mit der Funktion phpinfo() in einem Test-PHP-Datei einsehen.
