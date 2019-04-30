# Raspbian Lite einen Gesicht geben - Xfce und SDDM

Die Basis steht jetzt und können jetzt ungestört drauf aufbauen. Dieses Projekt hängt nur von [Kopflos](Kopflos) ab. Es wird kein anderer Projekt von diesem Projekt abhängen.

## Aber warum zeige ich hier nur eine Variante?

Es gibt schon viele breit gefächerte Tutorials. [Ein Beispiel dazu](https://lb.raspberrypi.org/forums/viewtopic.php?f=66&t=133691&sid=7651b71d6884cb46305f3a532bc0be07). Hier in meinem Anleitung geht es nur um eine vollständige Umsetzung einer Kombination und ich erkläre dabei meine Gedankengänge und Stolpersteine die ich auf dem Weg bis zum Endergebnis hatte.

### Warum Xfce?

Ehrlich gesagt war Xfce nicht von Anfang an meine erste Wahl. Ich wollte tatsächlich zuerst LXQt. Es klappte irgendwie einiges nicht so wie ich es erwartetet hatte. Einer meiner Gründe wahr, dass ich bereits einige Anwendungen auf Qt-Basis schon bereits kannte. Irgendwann schaute ich nochmal um und entdeckte einen Guide (in oben genannten Beispielquelle). Nach der Beschreibung gefiehl mir Xfce plötzlich mehr als LXQt. Da ich eh nochmal alles neu installieren musste, dachte ich mir, dann probier mal gleich was neues aus. Und schon wieder war mein Endergebnis nicht zufrieden stellend.

### Warum SDDM?

Nach längerem Suchen nach Lösungen für ein besseren Ergebnis, kam ich irgendwann dahinter, dass die Wahl des Desktop-Umgebung möglicherweise gar keinen Einfluss hatte auf meine Wünsche. Also probierte ich gezielt andere Desktop-Manager (DM). Denn es wurde immer automatisch Lightdm installiert. Da DMs ja nicht nur Desktops managed, sondern auch die Sessions verwaltet, habe ich angenommen, dass diese eventuell auch Einfluss auf Verhalten der Programme und Interaktionen mit dem Terminal haben. Ok, ich installierte also SDDM und dann Xfce, zack! Da war mein Ergebnis nahezu perfekt! Denn die DMs haben nämlich tatsächlich großen Einfluss auf das Verhalten der Programme und mein Terminal arbeitete plötzlich völlig anders. Ich konnte dann endlich so arbeiten wie ich auch gewohnt war. Nur eine Sache muss ich noch erwähnen: SDDM ist sehr Leistungshungrig und kann mit anderen Design für DM den Login und Session-Wahl erheblich verlangsamen. Dabei rede ich hier von Wartezeiten von mehr als 5 Minuten bis der Loginbildschirm überhaupt erscheint! Aber solange man SDDM nicht modifiziert, erscheint der Login-Bildschirm spätestens nach 20 Sekunden. Und das reicht für mich alle Male. Hauptsache ich hab meinen Desktop-Umgebung mit dem ich zufrieden bin.


## XServer, Xinit

Nun, Normalerweise werden die Abhängigkeiten automatisch geholt. Aber wir wollen gewisse Dinge vorweg nehmen und möglichst wenig Pakete zu installieren:
```bash
sudo apt-get install --no-install-recommends xserver-xorg xinit
```bash
Die Option "--no-install-recommends" sorgt dafür, dass die empfohlene Paketet nicht installiert werden. Und tatsächlich benötigen wir nur "xserver-xorg", "xinit" und deren direkte Abhängigkeiten, welches nach diesem Befehl installiert werden. "xinit" wird eigentlich nur benötigt, um die Desktop-Umgebung oder DM direkt aus der Shell starten zu können. Ansonsten scheint auch "xinit" überflüssig zu sein.


## Login und Desktop

Desktop-Manager ist tatsächlich optional. Denn man kann auch das Desktop von der Shell aus starten. Dazu braucht man sich nur einzuloggen und den Befehl "xinit".

### Desktop-Manager SDDM

In Kapitel [Warum SDDM?] habe ich bereits einiges dazu erklärt. Hier also nur der Befehl:
```bash
sudo apt install sddm
```
### Desktop-Umgebung Xfce

Hier installieren wir einfach nur komplett nacktes Desktop-Umgebung:
```bash
sudo apt-get install --no-install-recommends xfce4 xfce4-terminal blackbird-gtk-theme tango-icon-theme
```
"xfce4" selbst besitzt eine Anghängigkeit zu einem der Terminals. Lässt man "xfce4-terminal" weg, so wird automatisch xterm installiert. Da xterm nicht mein Geschmack ist und LxTerminal designtechnisch nicht so gut in Xfce einpasste, entschied ich mich für leichtgewichtigen Xfce-Terminal.

Apropo Design: Ohne einen Theme ist alles an GUI weiß. Das gefällt mir gar nicht. "blackbird-gtk-theme" fügt sich nahtlos in Xfce-Oberfläche ein. "blackbird-gtk-theme" empfehle ich auch immer mit "--no-install-recommends" zu installieren, weil sonst ein paar nicht funktionierende und viele weiße/helle Themes mitliefert werden. Xfce selbst liefert nur wenig an eigene Icons. Wenn die Icons nicht vollständig sind, dann sieht es eher unschön aus. "tango-icon-theme" passt sich gut dem Dark-Themes auf Xfce-Oberfläche an.

Nach Themes kannst du leicht mit dem folgenden Befehl suchen:
```bash
aptitude search gtk-theme icon-theme
```
Aber es gibt doch einige Themes, die manchmal nicht vollständig funktionieren bzw. nicht vollständig sind.

Viel mehr wird auch erstmal für die Grundlage des GUI nicht benötigt. Nach dem Neustart dürfte Login-Bildschirm automatisch erscheinen, voraus gesetzt man hat diese installiert. Ansonsten braucht man nur in Shell einzuloggen und den Befehl "xinit" nutzen.

#### Empfohlene Anwendungen für Xfce

Es ist nicht mehr viel was man noch braucht. Trotz minimale Anzahl an Paketen sind schon viele Sachen auf dem System möglich. Aber zu Xfce gibt es noch passende Tools:
```bash
sudo apt install meld mousepad ristretto xfce4-screenshooter thunar-volman libreoffice-l10n-de
```
Na gut Libre-Office ist vielleicht nicht optimal für Xfce auf Raspberry Pi. Aber ich konnte bisher noch keine besondere Nachteile feststellen. Hauptsache ich kann wie gewohnt arbeiten. Allgemein sind die Anwendungen nur Vorschläge und sollte eventuell die anstehende Sucherei ersparen.

Man beachte auch, dass dieses Kapitel noch überarbeitet wird. Ich habe das ganze nur auf die Schnelle notiert.
