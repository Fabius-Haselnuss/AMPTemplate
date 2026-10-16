# Life is Feudal: Your Own – AMP-Template

Dieses Repository enthält eine AMP-Vorlage für den **Life is Feudal: Your Own Dedicated Server**. Sie wurde mit einem **Ubuntu-ADS-Controller und einem Windows-ADS-Spielserver** eingerichtet und getestet. Die eigentliche Spielserver-Anwendung läuft unter Windows.

## Voraussetzungen und Abhängigkeiten

| Komponente | Erforderlich? | Zweck |
| --- | --- | --- |
| [CubeCoders AMP](https://cubecoders.com/AMP) mit ADS | Ja | Instanzverwaltung und Serversteuerung |
| Windows als Spielserver-Host | Ja, für diese getestete Vorlage | Führt `ddctd_cm_yo_server.exe` aus |
| SteamCMD | Ja, über AMP | Lädt die Dedicated-Server-Dateien (Steam-App-ID **320850**) |
| [MariaDB](https://mariadb.org/download/) oder kompatibler MySQL-Server | Ja | Speichert Spielwelt und Charakterdaten |
| SQL-Verwaltung, z. B. [HeidiSQL](https://www.heidisql.com/) | Empfohlen | Datenbank erstellen und SQL-Skript importieren |
| Microsoft Visual C++ Redistributable (x64) | Gegebenenfalls | Behebt fehlende Laufzeit-DLLs auf Windows |
| Netzwerk-/Firewallfreigaben | Für externe Spieler | Ermöglichen Spielerverbindungen |

**Datenbank-Version:** LiF:YO ist ein älterer Titel. Frühere Anleitungen nennen MariaDB 5.5 bzw. 10.2, die nicht mehr zeitgemäß sind. Neuere MariaDB-Versionen können funktionieren, sind aber nicht mit jeder LiF-Version oder Modifikation garantiert kompatibel. Eine unterstützte Version verwenden und das Datenbankschema sowie den ersten Serverstart testen.

**Sicherheit:** MariaDB-Port **3306 nicht öffentlich ins Internet freigeben**. Die Datenbank kann auf dem Windows-Spielserver selbst oder auf einem separat erreichbaren privaten Datenbankserver laufen. Bei separatem Host nur den Zugriff vom Spielserver erlauben.

## 1. AMP-Template einrichten

1. In AMP das Konfigurations-Repository `Fabius-Haselnuss/AMPTemplate` auf Branch `main` einbinden (AMP: **Configuration Repositories**).
2. **Fetch Latest** / Templates neu abrufen.
3. Eine neue Instanz mit der Vorlage **life-is-feudal-your-own** erstellen und als Ziel den **Windows-ADS-Host** wählen.
4. Die Instanz installieren und in AMP die **Update-Funktion** ausführen.

AMP lädt über SteamCMD die Windows-Dedicated-Server-Dateien mit App-ID **320850** herunter. Zusätzlich stellt das Template eine anfängliche `config_local.cs` bereit. Bereits vorhandene Konfigurationsdateien sollen beim Update nicht überschrieben werden.

> Das Template ist auf einen Windows-Spielserver ausgelegt. Eine Linux-/Wine-Spielserverinstallation wird durch diese Anleitung nicht als getestet oder unterstützt zugesichert.

## 2. MariaDB installieren und Datenbank erstellen

1. MariaDB auf dem Spielserver oder einem erreichbaren Datenbankserver installieren. Bei einer Installation auf demselben Host ist normalerweise **127.0.0.1:3306** die richtige Adresse.
2. Ein sicheres Administrationspasswort vergeben. Für den Spielserver nach Möglichkeit einen **eigenen Datenbankbenutzer mit nur den benötigten Rechten** verwenden, nicht dauerhaft den Datenbank-`root`-Benutzer.
3. Mit HeidiSQL oder einem anderen SQL-Client verbinden.
4. Die Datenbank **`lif_1`** anlegen. Auf eine zum verwendeten LiF-SQL-Skript passende UTF-8-Zeichenkodierung und Kollation achten; ältere Anleitungen verwenden z. B. `utf8_general_ci` bzw. `utf8mb3_general_ci`.
5. Im heruntergeladenen Spielserververzeichnis die Datei **`sql/new.sql`** suchen. Das Skript in die Datenbank `lif_1` importieren, sofern die Datenbanktabellen nicht bereits durch den verwendeten Serverstand erstellt wurden.
6. Prüfen, ob die Tabellen angelegt wurden und der Datenbankbenutzer darauf zugreifen kann.

**Achtung:** `new.sql` nur bei der Ersteinrichtung einer leeren Datenbank importieren. Nicht unüberlegt auf einer bestehenden Welt ausführen: Zuvor ein Backup anlegen.

## 3. Datenbank-Zugangsdaten in AMP einstellen

In der AMP-Oberfläche die drei Datenbankfelder ausfüllen:

| AMP-Feld | Beispiel bei lokaler MariaDB |
| --- | --- |
| Datenbankserver | `127.0.0.1:3306` |
| Datenbankbenutzer | Benutzername des angelegten Datenbankkontos |
| Datenbankpasswort | Passwort dieses Kontos |

Das Template schreibt diese Werte in die Datei **`config_local.cs`** des Spielservers:

```cs
$cm_config::DB::Connect::server = "127.0.0.1:3306";
$cm_config::DB::Connect::user = "lif_user";
$cm_config::DB::Connect::password = "HIER_NUR_EIN_BEISPIEL";
```

Diese Werte sind ausschließlich **Beispiele**. Das Passwort nur in AMP eintragen, **niemals ins öffentliche GitHub-Repository**. Nach dem Speichern die tatsächliche `config_local.cs` im Spielserververzeichnis kontrollieren, falls die Verbindung nicht funktioniert.

## 4. Spielwelt konfigurieren

Die AMP-Oberfläche bearbeitet Einstellungen aus **`config/world_1.xml`**, unter anderem Server-ID, Name und weitere verfügbare Parameter.

- Standardmäßig ist die Welt-ID **1** vorgesehen; sie muss zur Datei `world_1.xml` passen.
- Servernamen nicht länger als **63 Zeichen** wählen.
- Änderungen speichern und den Server gegebenenfalls neu starten.

## 5. Netzwerk und Firewall

Die aktuelle Template-Datei `life-is-feudal-your-ownports.json` definiert folgende Ports:

| Port | Protokoll laut Template | Verwendung |
| --- | --- | --- |
| 28000 | TCP und UDP | Spielport |
| 28001 | TCP und UDP | Weiterer Spiel-/Dienstport |
| 28002 | TCP und UDP | Weiterer Spiel-/Dienstport |

Die Windows-Firewall und – bei externem Zugriff – die vorgeschaltete Firewall bzw. Router-Portweiterleitung entsprechend der tatsächlichen Serverkonfiguration einrichten. Bei Bedarf die Portangaben mit `world_1.xml` abgleichen.

**Port 3306 ist ein Datenbankport, kein öffentlicher Spielerport.** Bei externer MariaDB nur eine private, abgesicherte Verbindung vom Spielserver zur Datenbank zulassen.

## 6. Erster Start und Funktionstest

1. Sicherstellen, dass MariaDB läuft und `lif_1` erreichbar ist.
2. In AMP prüfen, ob der Server-Download abgeschlossen ist.
3. Datenbankfelder und die Einstellungen in `world_1.xml` speichern.
4. Server in AMP starten.
5. Konsole und Logs auf SQL-Verbindungsfehler, fehlende DLLs und Startfehler kontrollieren.
6. Prüfen, ob der Server im Spiel erreichbar ist.

Der erste Start kann mehrere Minuten dauern. Fehlen DLLs, die passende **Microsoft Visual C++ Redistributable (x64)**-Laufzeit installieren. Für einen kontrollierten manuellen Shutdown verwendet der LiF-Server den Konsolenbefehl `quit();`.

## Fehlerbehebung

| Problem | Prüfen |
| --- | --- |
| Keine Datenbankverbindung | MariaDB-Dienst, Host/Port, Benutzer, Passwort, Datenbankrechte und Firewall |
| SQL-Tabellen fehlen | Existenz der Datenbank `lif_1` und Import von `sql/new.sql` |
| `config_local.cs` fehlt | AMP-Update erneut ausführen und Download-Log prüfen |
| Einstellungen werden nicht übernommen | AMP-Einstellungen speichern; Inhalt von `config_local.cs` und `config/world_1.xml` kontrollieren |
| Server startet wegen DLL-Fehlern nicht | Windows-Laufzeitbibliotheken / Visual C++ Redistributable prüfen |
| Server nicht erreichbar | Ports, Firewall, NAT/Portweiterleitung, IP und Serverlogs prüfen |

## Dateien des AMP-Templates

Alle sechs AMP-Dateien liegen absichtlich im **Repository-Hauptverzeichnis**, damit AMP die Vorlage zuverlässig einlesen kann:

| Datei | Aufgabe |
| --- | --- |
| `life-is-feudal-your-own.kvp` | AMP-Hauptvorlage, Pfade und Startparameter |
| `life-is-feudal-your-ownconfig.json` | Felder und Einstellungen in AMP |
| `life-is-feudal-your-ownmetaconfig.json` | Anbindung von XML und TorqueScript/KVP |
| `life-is-feudal-your-ownports.json` | Portdefinitionen |
| `life-is-feudal-your-ownupdates.json` | SteamCMD-Download und Konfigurationsdatei |
| `life-is-feudal-your-ownconfig_local.cs` | Ausgangskonfiguration der Datenbank |

## Quellen und weiterführende Anleitungen

- [LiF:YO-Installation unter Windows – FeudalTools](https://kb.feudal.tools/knowledge-base/setup-lifyo-dedicated-server-on-windows/)
- [LiF: Extended – Dedicated-Server-Setup](https://lifxmod.com/tutorials/life-is-feudal-your-own-dedicated-server.html)
- [SteamDB: LiF:YO Dedicated Server (App 320850)](https://steamdb.info/app/320850/config/)
- [CubeCoders AMPTemplates](https://github.com/CubeCoders/AMPTemplates)

---

**Projekt:** Fabius-Haselnuss · Life is Feudal: Your Own · Community-Template für CubeCoders AMP
