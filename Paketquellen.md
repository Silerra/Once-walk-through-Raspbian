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
Anschließend gibst du den folgenden Befehl ein:
```bash
sudo apt update && sudo apt upgrade
```
Jetzt kannst du beliebige Pakete von Buster-Quelle installieren und brauchst dann nur noch sowas wie ```bash sudo apt install -t buster <Paket>``` zu schreiben. Aber hier ist Vorsicht geboten! Denn nicht alles was im Buster zu finden ist, wird auf deinem Raspbian funktionieren! Also wähle diese Quelle nur mit Bedacht.