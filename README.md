# Once-walk-through-Raspbian
Einmal durch Raspbian laufen.

## Inhalt
- [Buster als weitere Quelle für Raspbian einrichten](#buster-als-weitere-quelle-für-raspbian-einrichten)
- [PHP 7.3 auf Raspbian installieren](#php-73-auf-raspbian-installieren)
- [Xdebug für PHP 7.3 installieren und einrichten](#xdebug-für-php-73-installieren-und-einrichten)
  - [Installation Xdebug](#installation-xdebug)
  - [Xdebug einrichten](#xdebug-einrichten)


## Buster als weitere Quelle für Raspbian einrichten
Die Softwarepakete auf Standardquellen sind leider nicht immer die aktuellsten. So war bei meiner Suche nach aktuellen PHP-Version nur die Version 5.6 auf Raspbian-Stretch-Repo vorhanden. (Jetzt mittlerweile 7.0) Möchte man jedoch eine aktuelleren Version installieren, so muss man erstmal die Quelle verfügbar machen:
```bash
file='/etc/apt/sources.list.d/buster.list' &&
echo "Erstelle die Datei '$file' mit dem Inhalt:" &&
echo 'deb http://mirrordirector.raspbian.org/raspbian/ buster main contrib non-free rpi
#deb-src http://mirrordirector.raspbian.org/raspbian/ buster main contrib non-free rpi
' | sudo tee $file && unset file
```
Nun, wenn man jetzt 'sudo apt update && sudo apt upgrade' machen würde, dann würde alle Pakete auf Raspbian ohne Rücksicht auf Komplikationen aktualisiert werden. Das würde dann darauf hinauslaufen, dass beim nächsten Neustart mit jede Menge fehlgeschlagenen Ladevorgänge starten würde und somit mehr oder weniger sich aussperrt.
Das wollen wir verhindern und setzen dem entsprechend Prioritäten welche Paketquellen bevorzugt behandelt werden:
```bash
file='/etc/apt/preferences.d/buster' &&
echo "Erstelle die Datei '$file' mit dem Inhalt:" &&
echo 'Package: *
Pin: release n=stretch
Pin-Priority: 900

Package: *
Pin: release n=buster
Pin-Priority: 750
' | sudo tee $file && unset file
```


## PHP 7.3 auf Raspbian installieren
Nachdem der Release Buster für Raspbian in Quellenverzeichnis verfügbar ist, kann man auf aktuellere Inhalte zugreifen wie z.B. PHP Version 7.3. Verfügbare Versionen kann man sich mit dem Befehl
```bash
apt-cache madison php
```
anschauen. Die Ausgabe wird dann so ähnlichen aus wie:
```bash
$ apt-cache madison php
       php |   2:7.3+69 | http://mirrordirector.raspbian.org/raspbian buster/main armhf Packages
       php |   1:7.0+49 | http://mirrordirector.raspbian.org/raspbian stretch/main armhf Packages
$ _
```
An diesem Beispiel kann man entnehmen, dass auf Raspbian Strech Repo die PHP-Version 7.0+49 verfügbar ist und auf Buster Repo die 7.3+69. Mit dem Befehl:
```bash
sudo apt install -t buster php
```
lässt sich jetzt einfach die aktuellere Version installieren. Aber sei gewarnt, dass alle davon abhängige Pakete ebenfalls mit Befehl 'sudo apt install -t buster' installiert werden müssen. Ansonsten werden die weitere Module mit der PHP-Version 7.3 nicht zusammen arbeiten können.


## Xdebug für PHP 7.3 installieren und einrichten
### Installation Xdebug
Nun, da jetzt die PHP-Version 7.3 installiert wurde, so möchte man auch eventuell für diese Version auch die entsprechende Debugger einrichten. Aber zuerst schauen wir die Abhängigkeiten mit folgenden Befehl an:
```bash
aptitude show php-xdebug
```
Da werden einige Informationen angegeben, wie z.B. ob es bereits installiert wurde, ob es automatisch geschah und viele andere Sachen. Uns ist jetzt aber nur die Abhängigkeit interessant und da steht wahrscheinlich sowas wie:
```bash
Hängt ab von: ucf, php-common (>= 1:7.0+33~), phpapi-20151012, libc6 (>= 2.4)
```
In diesem Beispiel würde jetzt mit normalen Befehl 'sudo apt install php-xdebug' php-common 7.0 und alle davon ausgehende Abhängigkeiten installieren. Es würde also bedeuten, dass die Pakete von PHP der Version 7.0 installieren würde. Somit hätten wir PHP-Versionen 7.0 und 7.3 auf Raspbian installiert. Xdebug wird dann nur auf 7.0 laufen können. Zudem unterstützt die Xdebug-Version 2.5 nicht die aktuelle PHP-Version 7.3 und somit wäre auch nicht möglich einfach mit symbolischen Link auch auf PHP 7.3 verfügbar zu machen. Siehe dazu https://xdebug.org/docs/install#versions unter PHP Version Support.

Nun, wir wollen aber, dass Xdebug unter 7.3 läuft, also geben wir den Befehl:
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
Hier sehen wir, dass Xdebug jetzt verfügbar ist. Aber bevor wir diese aktivieren, wäre vielleicht ein paar kleine Modifikationen nicht schlecht, damit wir in unserem IDE wie z.B. unter VSCode, Eclipse oder so, direkt nutzen können.
```bash
echo "Folgende Zeilen in Datei '$file' hinzufügen:" && echo '
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
' | sudo tee -a $file
```
mit folgenden Befehl sehen wir ob es geklappt hat:
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
Jetzt brauchen wir nur noch den Mod zu aktivieren:
```bash
phpenmod xdebug
```
Mit
```bash
php -v
```
können wir sehen, ob Xdebug erfolgreich aktiviert wurde.

Und das wars, jetzt können wir Xdebug in unserem IDE direkt nutzen. Der Standardport ist 9000. Weitere Informationen kann man leicht mit der Funktion phpinfo() in einem Test-PHP-Datei einsehen.
