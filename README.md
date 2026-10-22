# pocketSRT

**Mobile RTMP-zu-SRT/SRTLA/RIST-Bridge für Android – mit Bonding, adaptiver Bitrate, DJI-Auto-Connect und synchronisiertem Picture-in-Picture.**

pocketSRT empfängt RTMP-Streams von Kameras oder Streaming-Software und sendet das Programm als SRT, SRTLA oder RIST weiter. Der RTMP-Ingest läuft nativ auf dem Android-Gerät; Node.js, FFmpeg und externe Streaming-Hardware werden nicht benötigt.

## Download

[Neueste Version als APK herunterladen](https://github.com/romestylez/pocketSRT/releases/latest)

Erfordert Android 8.0 oder neuer und ein 64-Bit-ARM-Gerät (`arm64-v8a`). Die DJI-Integration erkennt Osmo Action 2, 3, 4, 5 Pro und 6, Osmo 360 sowie Osmo Pocket 3.

## Funktionen

- Nativer RTMP-Server für Actionkameras, OBS und andere RTMP-Quellen
- SRT-, SRTLA- und RIST-Ausgang mit adaptiver Bitratensteuerung
- SRTLA-Bonding über Mobilfunk, WLAN, Ethernet und zusätzliche [pocketBond](https://github.com/romestylez/pocketBond/)-Handys
- RIST-Bonding über die lokalen Mobilfunk-, WLAN- und Ethernet-Pfade mit automatischer
  Lastverteilung und eigener RIST-ABR
- DJI-Auto-Connect per Bluetooth LE für unterstützte DJI-Kameras
- Synchronisiertes Picture-in-Picture mit einer zweiten RTMP-Quelle
- Wechsel von Main und PiP im laufenden Stream mit Ton
- Vier frei wählbare Bild-/Ton-Szenen: Main/Main-Ton, Main/PiP-Ton, PiP/Main-Ton und PiP/PiP-Ton
- Einstellbare PiP-Synchronisierung, Position, Größe und 16:9-/9:16-Layout
- Optionale Steuerung unterstützter Black-Shark- und RedMagic-Smartphone-Lüfter
- Lokale REST API für Status, Steuerung und Telemetrie
- Live-Status, Bitratenanzeige und exportierbare Diagnose-Logs
- Oberfläche auf Deutsch und Englisch, umschaltbar im Hamburger-Menü

## Screenshots

<p align="center">
  <img src="screenshots/pocketsrt-ready.png" alt="pocketSRT 5.0.1 bereit zum Streamen" width="47%">
  <img src="screenshots/pocketsrt-live.png" alt="pocketSRT 5.0.1 während eines Bonding-Streams" width="47%">
</p>

## Signalweg

```text
Kamera / OBS ──RTMP──> nativer RTMP-Ingest ──> Media3 / MediaCodec
                                              │
                                              └──> MPEG-TS ──SRT/SRTLA/RIST──> Streaming-Ziel
```

Für PiP kommt eine zweite RTMP-Quelle hinzu:

```text
Main: rtmp://<handy-ip>:1935/live/stream ─┐
                                         ├──> synchronisiertes Programm ──> SRT/SRTLA/RIST
PiP:  rtmp://<handy-ip>:1935/live/pip ────┘
```

## Schnellstart

### 1. Streaming-Ziel eintragen

Unter **Menü → Stream**:

1. `SRT`, `SRTLA` oder `RIST` auswählen.
2. Ziel-URL eintragen, zum Beispiel:

   ```text
   srt://dein-server.com:5000?streamid=dein-key
   srtla://dein-server.com:5000?streamid=dein-key
   rist://dein-server.com:2030?username=dein-name&password=dein-passwort
   ```

3. Optional eine maximale Bitrate in kbit/s festlegen.

### 2. Hauptquelle verbinden

Die App zeigt die passende RTMP-Adresse für das aktuelle Netzwerk an. Diese Adresse als Streaming-Ziel in der Kamera oder Software eintragen und den RTMP-Stream starten.

Für DJI-Kameras kann unter **Menü → DJI Settings** WLAN, RTMP-Ziel und Auto-Start eingerichtet werden. pocketSRT konfiguriert und startet die Kamera anschließend per Bluetooth LE.

### 3. Stream starten

Sobald die Hauptquelle sendet, **Stream starten** antippen. Verbindungsstatus, aktive Bonding-Pfade und Ausgangsbitrate werden auf der Startseite angezeigt.

## Picture-in-Picture

PiP gehört in Version 5.0.0 zum Media3-/ExoPlayer-Ingest:

1. Unter **Menü → PiP** die Option **PiP aktivieren** einschalten.
2. Die zweite Quelle an die dort angezeigte PiP-Adresse (`/live/pip`) senden lassen.
3. Den Stream neu starten, damit die geänderte PiP-Konfiguration aktiv wird.

Main und PiP können während des Streams unabhängig als Bild- und Tonquelle gewählt werden. Die vier Szenen **Main · Main-Ton**, **Main · PiP-Ton**, **PiP · Main-Ton** und **PiP · PiP-Ton** schalten Bild und Ton gemeinsam auf derselben Zeitachse. Die PiP-Einstellungen erlauben außerdem einen manuellen Synchronisationsversatz, weiche Rollenwechsel sowie Änderungen an Position, Größe, Drehung, Rahmen und Seitenverhältnis.

Für einen stabilen PiP-Betrieb werden H.264/SDR, 720p und 25 oder 30 fps für die zweite Quelle empfohlen.

## Lüftersteuerung

pocketSRT kann unterstützte Black-Shark- und RedMagic-Lüfter per Bluetooth LE steuern. Die Einrichtung befindet sich unter **Menü → Lüfter**; dort wird zuerst der Hersteller ausgewählt.

- **Black Shark MagCooler 4 Pro:** Mute-, Overclocking- und Smart-Modus
- **Black Shark MagCooler 5 Pro:** fünf Intensitätsstufen und LED-Steuerung
- **Black Shark MagCooler/FunCooler 6:** Normal-/Silent-Modus und LED-Steuerung
- **Black Shark MagCooler 6 Max:** Overclocking-, Smart- und Silent-Modus
- **Black Shark MagCooler 6 Pro mit Display:** Modus- und LED-Steuerung; die Temperaturautomatik wechselt mangels zuverlässigem Aus-Befehl zwischen einem frei wählbaren Idle- und Kühlmodus
- **RedMagic VC Cooler 6 Pro:** Lüfterstufen 1–9, LED-Steuerung und Diablo-Modus
- Automatisches Ein- und Ausschalten anhand frei wählbarer Start- und Stopptemperatur
- Anzeige der aktuellen Gerätetemperatur sowie des Verbindungs- und Automatikstatus
- Manuelles Ein- und Ausschalten, wenn die Temperaturautomatik deaktiviert ist

Die getrennten Start- und Stopptemperaturen bilden eine Hysterese und verhindern dadurch ständiges Ein- und Ausschalten nahe der Grenztemperatur. Die Regelung verwendet direkt die lokale Gerätetemperatur; die kompatible `/api/blackshark`-Schnittstelle bleibt für Fernanzeige und Fernsteuerung erhalten.

## SRTLA und pocketBond

SRTLA verteilt den Stream über mehrere Netzwerkpfade. pocketSRT kann auf einem Gerät Mobilfunk, WLAN und Ethernet als getrennte Pfade verwenden.

Zusätzliche Android-Handys lassen sich mit [pocketBond](https://github.com/romestylez/pocketBond/) als weitere Mobilfunkpfade einbinden:

1. pocketBond auf den Hilfs-Handys installieren.
2. In pocketSRT und pocketBond dasselbe Passwort eintragen.
3. Alle Geräte mit demselben lokalen WLAN verbinden.
4. Auf jedem Hilfs-Handy einen aktiven Mobilfunk-Datentarif bereitstellen.

Wenn die Kamera ein WLAN ohne Internet bereitstellt, kann je nach Android-Gerät die Entwickleroption **Mobile Daten immer aktiv** oder ein zusätzlicher Ethernet-Uplink erforderlich sein.

## RIST-Bonding

RIST verteilt den Stream über die verfügbaren lokalen Netzwerkpfade. pocketSRT bindet jeden
RIST-Peer vor dem ersten Paket an das zugehörige Android-Netzwerk, verteilt die Last adaptiv
und steuert die Encoderbitrate mit einer eigenen RIST-ABR. Pfadverlust und Rückkehr werden im
laufenden Stream verarbeitet, ohne still auf SRT oder SRTLA umzuschalten.

Als praktisch geprüfter Empfänger kann
[moo-rist-hosting-docker](https://github.com/moo-the-cow/moo-rist-hosting-docker)
verwendet werden. pocketSRT ist nicht an diesen Container gebunden: Andere standardkonforme
RIST-Main-Profile-Empfänger funktionieren ebenfalls, sofern Listenerrolle, UDP-Port,
Authentisierung und die Zusammenführung mehrerer Peers desselben Flows passend konfiguriert
sind. Die vollständige Architektur- und Abnahmedokumentation steht unter
[docs/rist-produktionsplan.md](docs/rist-produktionsplan.md).

## Monitoring und Logs

Die Startseite zeigt den Status des RTMP-Eingangs, des SRT-, SRTLA- oder RIST-Ausgangs und der einzelnen Bonding-Pfade sowie deren aktuelle Datenrate. Unter **Menü → Log** stehen ausführliche Diagnoseinformationen und der Log-Export zur Verfügung.

## Lokale REST API

Die lokale API läuft auf Port `7788` und wird im Netzwerk per mDNS als `_pocketsrt._tcp.` angekündigt. PocketChat kann mit `POST /api/snapshot` einen Snapshot des vollständigen Programmbilds abrufen. Der Request benötigt einen leeren Body mit `Content-Length: 0`. Das Foto enthält Main, PiP und die aktive Privatsphäre-Unschärfe, aber keine Bedienelemente.

Eine erfolgreiche Antwort hat HTTP-Status `200`, den Content-Type `image/jpeg` und liefert das Foto in voller Program-Auflösung direkt im Response-Body. Dateiname und Abmessungen stehen zusätzlich in `Content-Disposition`, `X-Snapshot-Width` und `X-Snapshot-Height`. PocketChat speichert diese JPEG-Daten auf seinem eigenen Gerät. Ist gerade kein Snapshot möglich oder läuft bereits einer, antwortet die API mit `409`; bei einem Zeitablauf mit `503` und bei einem Aufnahme- oder Kodierungsfehler mit `500` und einer JSON-Fehlerantwort.

## Credits

| Projekt | Verwendung in pocketSRT | Lizenz |
|---|---|---|
| [srtdroid](https://github.com/ThibaultBee/srtdroid) | SRT-Protokoll und SRT-Ausgang | Apache-2.0 |
| [libRIST](https://code.videolan.org/rist/librist) | RIST-Main-Profile-Transport, Bonding und Wiederherstellung | BSD-2-Clause |
| [AndroidX Media3](https://github.com/androidx/media) | ExoPlayer-basierter FLV/RTMP-Ingest, Decoder und Wiedergabezeitachse | Apache-2.0 |
| [Moblin](https://github.com/eerimoq/moblin) | Referenz und übertragene Logik für DJI BLE, SRTLA, adaptive Bitrate und nativen RTMP-Ingest | MIT |
| [HaishinKit](https://github.com/HaishinKit/HaishinKit.swift) | Referenz für Teile der nativen RTMP-Protokollimplementierung | BSD-3-Clause |
| [romestylez/BlackSharkLib.swift](https://github.com/romestylez/BlackSharkLib.swift) | BLE-Protokollreferenz; Fork von [Spillmakers Original](https://github.com/Spillmaker/BlackSharkLib.swift) mit zusätzlicher, hardwaregeprüfter Unterstützung für den FunCooler 6 | MIT |
| [RedmagicCoolerIntegration](https://github.com/Bittersweet1987/RedmagicCoolerIntegration) | Interoperabilitätsreferenz für die eigenständige RedMagic-GATT-Implementierung; kein Quellcode übernommen | GPL-3.0 |

Weitere Abhängigkeiten und Lizenzhinweise stehen in [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md).

## Support

- [Ko-fi](https://ko-fi.com/romestylez)
- [GitHub Sponsors](https://github.com/sponsors/romestylez)
