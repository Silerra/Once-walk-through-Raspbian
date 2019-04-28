# Kopflos - Raspbian Lite als Basis

In diesem Kapitel geht es um die grafische Oberfläche und deren Anwendungen. Denn was man mit Raspberry Pi super machen kann, sind neue Dinge auszuprobieren! Aber was man bei Raspberry Pi nicht hat, sind genügend Ressourcen! Also stößt man hier doch sehr schnell an die Grenzen, wenn man z.B. versucht einfach Ubuntu zu nehmen und dann Pakete einem nach dem anderem draufzupacken, um halt verschiedene grafische Oberflächen (KDE, LXQT usw.) auszuprobieren oder auch andere Login-Varianten bzw. Display-Manager (gdm, kdm, usw.) zu testen. Erstens wird der Speicher irgendwann knapp und zweitens ist sowas auf Raspberry erst gar nicht möglich. Zumindest nicht auf dieser Weise. Damit ist man dann auch schnell in der Meinung, dass es nicht so viele Möglichkeiten bestehen. Falsch! Gerade bei Raspberry Pi gibt es mittlerweile unvorstellbare viele Möglichkeiten! Für diesen kleinen Einplatinen-Computer gibt es schon bereits über 50 verschiedene Betriebssysteme:

> Neben Raspbian Desktop, gibt es noch Raspbian Full, Raspbian Lite, DietPi, Ubuntu Mate, Snappy Ubuntu Core, Kano OS, PiNet, Max2Play, IchigoJam BASIC RPi1.2, Arch Linux mit GNOME 3.2, Kali Linux (kali1, kali2), Alpine Linux, Fedora, FedBerry, CentOS, OpenSUSE, gentoo (aarch64), OpenWRT, OctoPi, Volumnio, moOde, RuneOS, OpenELEC (openelec_rpi1, openelec_rpi2), LibreELEC (LibreELEC_RPi, LibreELEC_RPi2), OSMC (OSMC_Pi1, OSMC_Pi2), Recalbox (recalboxOS-rpi1, recalboxOS-rpi2), RetroPie (Retropie1, Retropie2), Lakka (Lakka_RPi, Lakka_RPi2), Risc OS, Windows 10 IoT Core (Stable, Preview), Windows 10 ARM (WoA), RaspBSD, NetBSD, HypriotOS, ResinOS (resinOS_RPi1, resinOS_RPi1), balenaOS, ChibiOS/RT, FreeRTOS, Raspand, AROS, Tizen 3.0, Plan 9, Pidora, ChromiumOS for SBC, FirefoxOS
> - Quelle: https://www.elektronikpraxis.vogel.de/45-betriebssysteme-fuer-den-raspberry-pi-a-488934/

> AIYprojects, Amibian, Arch1, Arch2, batocera_RPi1, batocera_RPi2, CStemBian, Flint OS, Gladys, lede2RPi1, lede2RPi2, lineage, OpenPlotter, Pi MusicBox, picoreplayer, picoreplayerAudio, PiTop, quirky, raspberry-vi, Raspex, RasPlex_RPi, RasPlex_RPi2, RetroX Frambu, rtandroid, Screenly OSE, solydx, TLXOS, void1, void1_musl, void2, void2_musl, XBian_RPi1, XBian_RPi3
> - Quelle: https://github.com/procount/pinn/wiki/OSes-supported-by-PINN)

Für Raspbian stehen auch mindestens zwei verschiedene OS-Installer zur Verfügung (BerryBoot, NOOB, PINN, WD PiDrive). Zudem existieren noch verschiedene Versionen von Bootloader, welches auch eine Art Firmware für Raspberry Pi darstellt.

Jetzt schon zu viel des Guten? Nein, ich bin noch am Anfang und könnte noch ganze Menge andere Sachen aufzählen. Vielleicht kommt das noch. Zurück zu Angelegenheiten des Raspberry Pi's: Ich gehe jetzt Schritt für Schritt durch und ordne jedes Thema in eigenen Unterkapitel zu. So weit es geht natürlich!


## OS-Installer und WLAN-Conf

Wie in der Einführung schon erwähnt, gibt verschiedene OS-Installer. Der bekannteste dürfte immernoch NOOB sein und wird gern mit Betriebssystem Raspbian Full geliefert. Dieses NOOB-Variante belegt aber schon einiges an Speicherplatz. Nun die OS-Installer bieten im Normalfall eine einfache Möglichkeit WLAN einzurichten und so wird dann die "Full"-Variante beinahe überflüssig. Ich sage deshalb "beinahe", weil diese Full-Version evtl. doch benötit wird. Nämlich dann wenn man versucht ohne Hilfe eines anderen PCs in Hidden-WLAN einzuloggen. (Über GUI ist mir bisher weder bei NOOB noch PINN gelungen.) Selbst unter Raspbian klappt das mit Hidden-WLAN nicht so einfach. Aber zum Glück reicht eine Konfigurationsdatei aus die man sogar leicht anlegen kann:
```bash
file='/etc/wpa_supplicant/wpa_supplicant.conf' &&
sudo echo -e "\033[36m\nErstelle die Datei '$file' mit dem Inhalt:\033[0m" &&
echo 'ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=DE

network={
	ssid="<dein ssid>"
	scan_ssid=1
	psk="<dein Wlan-Passwort>"
}
' | sudo tee $file && sudo chmod 644 $file && unset file
```
Da wir unter einem OS-Installer ebenfalls WLAN benötigen werden, braucht es eigentlich nur die selbe Datei in entsprechenden Verzeichnis abgelegt zu werden. Das entsprechende Verzeichnis liegt aber auf eine andere Partition. Diese Partition trägt normalerweise den Namen "/dev/mmcblk0p5". Es ist auch möglich, dass die Settings-Partition einen anderen Namen trägt. Also schauen wir mit folgenden Befehl nach:
```bash
lsblk --output NAME,SIZE,TYPE,MOUNTPOINT,LABEL
```
Wir brauchen die Partition mit dem Label "SETTINGS". Eventuell muss Mount-Befehl noch angepasst werden. Zeigt die Ausgabe des lsblk-Befehls bei SETTINGS-Partition einen Mountpoint an, dann ist der nachfolgende Befehl überflüssig:
```bash
sudo mkdir /media/settings && sudo mount /dev/mmcblk0p5 /media/settings
```
Anschließend brauchst du die Datei nur noch zu kopieren:
```bash
sudo cp /etc/wpa_supplicant/wpa_supplicant.conf /media/settings/
```
Möglicherweise müsste der grade genannte Befehl angepasst werden und der mount-Befehl kann in aktuelleren Versionen von Raspbian eventuell übersprungen werden, da dieser bereits nach der Installation von Raspbian automatisch eingebunden ist.
Jetzt braucht man nur noch den Raspbian dazu bringen die aktuelle Konfiguration zu laden:
```bash
sudo systemctl reenable wpa_supplicant.service
sudo systemctl restart wpa_supplicant.service
```
Ehrlich gesagt habe ich die grad genannte systemctl-Befehle noch nicht getestet, da ich selber nie benötigt habe. Möglicherweise müsste man in der GUI noch <dein ssid> auf WLAN-Symbol anklicken, damit von da an automatisch verbindet. Aber spätestens nach einem Neustart wird sich OS-Installer sowie das Raspbian automatisch verbinden. Selbst wenn man das Betriebssystem neu installiert, wird diese Conf-Datei die wir grade nach settings verschoben haben automatisch in jeweiligen Installation eingefügt. (Zumindest ist es bei PINN so.) Der Grund also warum ich es nur einmal einrichten brauchte.


## NOOB ohne PC ersetzen

Ich werde dazu noch die deutsche Übersetzung schreiben. Aber jetzt gibt es erstmal nur einen Link, wo du nachschauen kannst: https://lb.raspberrypi.org/forums/viewtopic.php?t=212452

Ich selber bevorzuge PINN, da ich damit gute Erfahrung gemacht habe. PINN bietet mehr Optionen als NOOB und man kann leichter Einstellungen kopieren, was bei NOOB nicht möglich ist. Die Auswahl an Betriebssystemen sind größer und wenn man möchte, kann man sogar eigene Repos angeben. Es kommen regelmäßig Updates und kann sich sogar selbst automatisch aktualisieren. PINN kann man sich hier holen: https://github.com/procount/pinn


## Vorbereitung - Raspbian Lite

Du kannst dich durch verschiedene Betriebssysteme probieren. Aber eine Sache ist auffällig: Es werden viele Derivate von Raspbian angeboten, wobei Raspbian selbst ein Derivat von Debian ARMHF-Version ist. (https://www.debian.org/distrib/netinst) Der große Vorteil an Raspbian ist, dass es komplett eigenen Dokumentationen, Support, Repositories usw. gibt. Wenn du was suchst reicht schon das Schlüsselwort "raspbian" aus. Wörter wie "debian" oder "ubuntu" können dir ähnliche Ergebnisse liefern. (Ich nutze z.B. gerne "ubuntu" in Kombination mit Befehlen wenn ich deren Funktionalitäten wissen möchte, da es einen sehr ausführlichen und sogar nahezu vollständige deutsche Fassung dazu gibt.)

### Aber warum geht hier mit Raspbian Lite weiter?

Der Grund und meine Motivation ist ganz einfach: Ich möchte Ressourcen sparen soweit wie es geht, aber auch komplett was anderes an grafische Oberfläche haben. Die Desktop-Umgebung die bei Raspbian Full oder Raspbian Recommend mitgeliefert wird, ist eine angepasste Version des LXDE. Ich wollte die LXDE-Oberfläche modifizieren und bekam ständig neue Probleme. So zerflückte ich das System immer mehr und mehr und es kamen weitere Probleme auf während ich eine gelöst hatte. Zudem gab es auch noch extrem störende Verhalten welches aus dem LX-Panel hervorging. So wurde z.B. meine angelegte .asoundrc-Datei ständig überschrieben. Habe ich Schreibschutz draufgelegt, so stürzte LX-Panel ab, wenn ich auf Lautsprecher-Symbol ging. Da ich eben auch anwendungstechnisch auch meine Vorzüge und vieles ausprobiert habe, bleibe ich auf Basis von Raspbian Lite. Ich könnte auch zu Arch wechseln. Aber da ich schon bereits sehr viel von Raspbian wusste und auch sehr leicht ist an die Problemlösungen ranzukommen, wagte ich den Wechsel nicht und fand meinen optimalen Ergebnis schon bereits wiederholt auf Raspbian Lite.

### Vorbereitung und Anpassungen

Nun, nachdem Raspbian Lite installiert wurde, empfehle ich schon hier die ersten grundlegende Sachen festzulegen. Dazu logst dich als User "pi" ein mit dem Passwort "raspberrz" (statt "raspberry"). Die Tasten "z" und "y" sind noch vertauscht. Wird aber schon in nächsten Schritten behoben. Jetzt dazu der Befehl:
```bash
sudo raspi-config
```
Vielleicht wünschst du dir einen anderen Hostnamen. Also gehst du auf "2 Network Options" und dann "N1 Hostname". Da wählst du einen anderen Namen. Diese Option ändert in Wirklichkeit nur zwei Dateien: "/etc/hostname" und "/etc/hosts"

Für deutsch-sprachigen Raum wäre entsprechende regionale Einstellungen praktisch. Diese findest du unter "4 Localisation Options".
Fangen wir mit Einstellungen der Zeichensätzen an. Dazu gehst du auf "I1 Change Locale", scrollst runter und wählst mit Leertaste einmal "de_DE.UTF-8 UTF-8" und optional noch "en_US.UTF-8 UTF-8" aus. Anschließend einmal TAB-Taste drücken und dann Enter. Es folgt eine weitere Abfrage und die zuvor ausgewälten Einstellungen tauchen neben "C.UTF-8" auf. Diese Einstellung wirkt auf das ganze System aus. Wählst du "Keine" aus, so entscheiden die Anwendungen welche Sprache dieser nutzen. "C.UTF-8" ist eine Fallbacklösung und wenn diese ausgewählt ist, erscheint eventuell Entwicklersprache. Diese ist häufig noch nicht mal auf Englisch. (Installiert man z.B. Wamp [wenn diese überhaupt für Raspbian existiert] so erscheint diese auf französisch.) Wählst du "de_DE.UTF-8" aus, dann wird für das ganze System automatisch deutsche Sprache bzw. Übersetzung ausgewählt. "en_US.UTF-8 UTF-8" das ganze in us-englischer Sprache bzw. Übersetzung. Zudem wenn man "de_DE.UTF-8 UTF-8" auswählt, dann wird in folgende Reihenfolge priorisiert: "de_DE.UTF-8 UTF-8", "en_US.UTF-8 UTF-8" und dann erst "C.UTF-8".

Unter "4 Localisation Options" kannst du auch schon die Zeitzone setzen, sodass die Uhrzeit auch direkt richtig angezeigt wird. Hier wählst du dann "I2 Change Timezone" dann Europe anschließend Berlin für deutsche Zeit. "I3 Change Keyboard Layout" ist eher uninteressant und wird nicht wirklich benötigt. Den entsprechenden Zeichensatz auf der Tastatur haben wir bereits durch "I1 Change Locale" erheblich verändert. "I4 Change Wi-fi Country" ist auch überflüssig, da wir bereits durch die Datei "wpa_supplicant.conf" in unserem Settings-Partition eingerichtet hatten. (Kapitel [OS-Installer und WLAN-Conf](#os-installer-und-wlan-conf))

Unter "5 Interfacing Options" wird's spannend. Hier kann man verschiedene Module ein- bzw. ausschalten. Die am häufigsten gefragte Option ist natürlich SSH. Für grafische Fern-Zugriff eignet sich VNC (dazu benötigt man VNC-fähiges App auf Endgerät, mein Windows 10 Phone kann das nicht). Die anderen Optionen sind entweder selbsterklärend oder für Leute mit speziellen Vorhaben.

"6 Overclock" ist für 3B+ eher nutzlose Option. Die Fehlermeldung beschreibt es ja, wenn man drauf geht. Interessanterweise ist dies aber auf anderem Wege möglich. Aber meines Erachtens nicht notwendig, da erstens bei Last sich automatisch hoch taktet und zweitens nicht wirklich Vorteile bringt, wenn man nicht genug schnelle Speicher besitzt. Bei Raspberry Pi 3B+ ist es nur 1GB RAM welches auch teilsweise durch Grafik-Einheit genutzt wird.

Unter "7 Advanced Options" gibt es mindestnes noch wirklich eine interessante Option: "A3 Memory Split" hat nämlich Einfluss auf CPU-Last. Ist der Speicher für Grafik-Einheit zu klein, dann steigt die GPU und indirekt CPU-Last, da es dann häufiger die grafische Elemente neu berechnen muss.

Zuletzt ist unter "7 Advanced Options" noch eine weitere Option von Bedeutung: "A7 GL Driver" wird "G2 GL (Fake KMS)" für arm64-Projekt benötigt. KMS ist ein OpenGL-Desktop-Treiber und kann von OpenGL aus entweder Kernel-KMS-Treiber (Full KMS) oder DispmanX API for composition (Fake KMS) verwendet werden. Da beim arm64-Projekt Video-Playback zum Einsatz kommt, macht es wenig Sinn über Kernel laufen zu lassen, wenn man die Bilder direkt an Bildschirm schicken kann. (Quelle: https://www.raspberrypi.org/forums/viewtopic.php?t=192017)

Somit wäre man mit Raspi-Konfiguration durch und können einfach mit Escape-Taste verlassen. Jetzt wäre dringend notwendig das System auf aktuellen Stand zu bringen. Aber vorher noch eine Anpassung. Nicht das wir die Arbeit doppelt machen.
```bash
sudo nano /etc/apt/sources.list
```
und ändern die Repository:
```bash
deb http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi
```
wir ersetzen "raspbian.raspberrypi.org" durch "mirrordirector.raspbian.org":
```bash
deb http://mirrordirector.raspbian.org/raspbian/ stretch main contrib non-free rpi
```
und anschließend drücke strg+x und bestätige mit "j" und speichere unter gleichen Datei-Namen. Jetzt noch:
```bash
sudo apt update && sudo apt full-upgrade
```
Somit werden die Updates über entprechende topologisch nächstliegende Spiegel-Server aktualisiert. Das kann bei zahlreichen Paketen doch recht ordentlichen zeitlichen Unterschied machen. Bestimmt denkst du jetzt wurde alles aktualisiert. Nein, es folgen noch zwei weitere Befehle:
```bash
sudo apt-get dist-upgrade
```
Wobei dist-upgrade eventuell überflüssig ist. Aber sicher ist sicher. Jetzt noch:
```bash
sudo rpi-update
```
Und ja, hier folgt garantiert noch ein Firmware-Update (wenn du nicht vor kurzem gemacht hast). Das ist nämlich das was vor dem PINN geladen wird (Rainbow-Screen). Vor dem Reboot noch einen Passwort für root setzen. Aber Vorsicht mit dem Sonderzeichen!:
```bash
sudo passwd root
```
Und jetzt Gerät neustarten z.B. mit Befehl "reboot". Nach dem Neustart logst du dich als "root" und gibst den zuvor festgelegten Passwort ein.

Denn nur von hier aus ist eine Änderung an den Benutzernamen möglich. Man kann zwar einen neuen Benutzer anlegen. Aber dann könnte das arm64-Projekt fehlschlagen. Da mit dem Befehl "adduser" einen neuen User-ID festgelegt wird. Zudem ist es von hier aus eher umständlich die ganzen Gruppen einzeln einzutippen oder gar langen komplexen Befehl zu nutzen. Da der neue Nutzer keine Gruppen besitzt außer sich selbst. Achte auch darauf, dass für SSH-Verbindung das gleiche gilt. Wenn du via SSH arbeitest, sollte alle Verbindung mit "pi"-Login getrennt werden. Aber auch das Autologin in raspi-config sollte ausgeschaltet sein. Da es sonst automatisch via "pi" in den Desktop-Umgebung bzw. Shell einloggt und somit die nachfolgende Schritte nicht mehr funktionieren.

Hast du dich dann erfolgreich als "root" eingeloggt, dann kannnst du dein eigenes Nutzernamen festlegen. Die folgenden Befehlen ersetzt du newUsername mit deinem neuen Nutzername:
```bash
usermod -l newUsername pi
usermod -m -d /home/newUsername newUsername
groupmod -n newUsername pi
passwd newUsername
logout
```
Neben setzen neuen Username macht auch Sinn den Home-Verzeichnis und den Gruppennamen ebenfalls zu ändern. Wobei Gruppenname eher optional ist. Für arm64-Projekt sollte aber der Home-Verzeichnis, Nutzername und Gruppenname gleich sein, um mögliche Probleme zu vermeiden. Die Passwort-Änderung machte vorher nur wenig Sinn, da der Zeichensatz für die Tastatur erst nach dem Local-Settings, Update und Neustart auf deutschem Layout gesetzt wird.

Nach dem Logout, kannst du dich mit neuen Nutzernamen einloggen. Passwort für root brauchen wir nicht mehr:
```bash
sudo passwd -d root
```

Somit wäre die komplette Vorbereitung abgeschlossen. Einige der nachfolgenden Projekte werden von diesen Vorbereitungen profitieren bzw. manche davon werden darauf aufgebaut!
