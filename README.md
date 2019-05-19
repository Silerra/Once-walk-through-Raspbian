# Once-walk-through-Raspbian
Einmal durch Raspbian laufen.

# Vorwort

In diesem Projekt habe ich vor eine Art Walkthrough für Home-Entertainment und Webentwicklung zu beschreiben. Es kursieren extrem viele Schnippseln für einen vollständigen Lösung im Internet. Einige Projekte/Lösungnen funktionieren nicht, andere sind teilweise wie Duplikate mit unterschiedlichen Auslegungen und wiederum andere Projekte/Lösungen könnte der fehlende Baustein für Komplett-Paket sein die nicht einfach zu finden sind.
Hier soll alles auf ein einziges Projekt zusammen gefasst werden. Die Schwerpunkte dabei sind:
- Möglichst Optimale Lösung zu ermöglichen.
- Das Linuxsystem von strukturellen Aufbau her besser zu verstehen.
- Selbst eine Lösung auf einfache Art und Weise zu erhalten.
- Wissen wo die Fallstricken sind.
- Die Wahrscheinlichkeit doppelte oder mehrfach Ausführungen von Diensten zu minimieren.

Das Projekt wird ziemlich umfangreich werden. Ich werde versuchen die Themen möglichst inhaltlich getrennt zu halten, um zum einem einzelne Punkte leichter überspringbar zu machen und um zum anderem die Orientierungsmöglichkeiten einfach zu halten.

Es wird sich auf jedem Fall lohnen sich einmal anzuschauen welche Themen dieses Projekt enthält bzw. enhalten wird. So wünsche ich dir viel Spaß beim lernen!

Viele Grüße
Silerra


#### Updates
Es gibt jetzt die Möglichkeit die Basis automatisch durchlaufen zu lassen. Es ist im Prinzip ein automatischer Installer bzw. Konfigurator. Damit kann man auf die schnelle grundlegende Konfigurationen wie Host-/ Nutzernamen ändern, Sprachkonfiguration und automatischer Update.
```bash
wget -i https://owtr.silerra.de/file-list && sudo bash base-config
```


TODO:
- Noob vs. Pinn
- Es gibt nicht nur Raspbian
  - Multimediasysteme
  - Back to the past: Emulatoren-OS
  - Schlank geht auch
  - Risiko und Forschung
- Mirrordirector
- Headless vs. Standard
- Raspbian Sessions und meine Erfahrungen (lightdm, sddm)
- Beispiel Xfce4
  - Empfehlungen Lite-Projekte
  - Meine Favoriten
- Der Wurm in Bluetooth mit dem Soundsystem
  - Schichten: hci, bluetooth, bluealsa, ...
  - Dienst Bluetooth --noplugin=sap in /lib/systemd/system/bluetooth.service
  - Alsa vs. Pulseaudio
  - .asoundrc, asound.conf
  - Projekt pi-btaudio oder geht das noch anders?
  - Projekte librespot und raspotify
- Git, Apache, einfache Sachen halt
- Batch, oh was haben wir da: Oh my ZSH
  - Terminals, Shells
  - Bash-Aliases, Konfigurationsdateien
- Viele Möglichkeiten bei Node, aber welche ist das Beste?
  - Projekt nodejs.org direkt an der Quelle, ist sie das wirklich?
  - Nodesource - Nur Manuell
  - Oh ja, buster geht auch: Kompabilitätsprobleme
- IDE - Entwickeln wie ein Profi
  - Editor vs. IDE
  - Aptana, Eclipse, VSCode, Sublime-Text und Co.
  - Debuggingschnittstellen und grafische Darstellung Git
  - Plugins
  - Derivate Code
    - armhf-Pakete
    - Der normale Weg und der akutelle Stand apt-mark: hold
- Es wird eng auf dem System
  - fstab
  - linux-swap
  - gparted
  - Projekt rpi_zram


## Inhalt
- [Vorwort](#vorwort)
- [Buster als weitere Quelle für Raspbian einrichten](Paketquellen#buster-als-weitere-quelle-für-raspbian-einrichten)
- [PHP 7.3 auf Raspbian installieren](#php-73-auf-raspbian-installieren)
- [Xdebug für PHP 7.3 installieren und einrichten](#xdebug-für-php-73-installieren-und-einrichten)
  - [Installation Xdebug](#installation-xdebug)
  - [Xdebug einrichten](#xdebug-einrichten)
