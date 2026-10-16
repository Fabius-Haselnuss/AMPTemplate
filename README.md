# Life is Feudal: Your Own – AMP Template

Community-Template für den Dedicated Server von **Life is Feudal: Your Own** in [CubeCoders AMP](https://cubecoders.com/AMP).

## Dateien

Die AMP-Vorlage liegt bewusst im **Hauptverzeichnis** des Repositories, damit AMP sie als Konfigurations-Repository abrufen kann.

| Datei | Zweck |
| --- | --- |
| `life-is-feudal-your-own.kvp` | Hauptvorlage für AMP |
| `life-is-feudal-your-ownconfig.json` | Einstellungen in der AMP-Oberfläche |
| `life-is-feudal-your-ownmetaconfig.json` | Zuordnung der Konfigurationsdateien |
| `life-is-feudal-your-ownports.json` | Portdefinitionen |
| `life-is-feudal-your-ownupdates.json` | Download und Aktualisierung des Servers |
| `life-is-feudal-your-ownconfig_local.cs` | Ausgangsdatei für die Datenbankkonfiguration |

## Installation in AMP

1. In AMP unter **Configuration → Instance Deployment → Configuration Repositories** das Repository `Fabius-Haselnuss/AMPTemplate:main` hinzufügen.
2. **Fetch / Fetch Latest** ausführen.
3. Eine neue Instanz mit der Vorlage **life-is-feudal-your-own** erstellen.
4. Auf dem Windows-Host die Serverdateien über die AMP-Updatefunktion herunterladen.
5. Datenbankserver, Benutzer und Passwort in der AMP-Oberfläche konfigurieren.

> Die Vorlage wurde in einer Umgebung mit Ubuntu-ADS-Verwaltung und Windows-ADS-Spielserver entwickelt. Sie ist nicht als Linux-Spielserver-Installation getestet.

## Datenbankkonfiguration

Die AMP-Felder für Datenbankserver, Benutzer und Passwort werden über die KVP-Metakonfiguration auf `config_local.cs` abgebildet. Die Datei verwendet das TorqueScript-Format:

```cs
$cm_config::DB::Connect::server = "127.0.0.1:3306";
$cm_config::DB::Connect::user = "root";
$cm_config::DB::Connect::password = "";
```

Dies sind **Beispielwerte**. Die tatsächlichen Zugangsdaten müssen in AMP eingetragen werden. Zugangsdaten niemals in das öffentliche GitHub-Repository hochladen.

Beim Update wird eine Ausgangsdatei bereitgestellt, ohne bestehende Konfigurationsdateien absichtlich zu überschreiben.

## Hinweise

- Nach Änderungen an der Vorlage in AMP **Fetch Latest** ausführen.
- Bei Änderungen an Dateinamen oder internen Pfaden kann eine neu erstellte Instanz erforderlich sein.
- Eine bestehende Serverinstallation vor größeren Änderungen sichern.
- Das Verschieben der AMP-Template-Dateien in einen Unterordner wird nicht empfohlen: Die [offiziellen AMPTemplates-Beitragsregeln](https://github.com/CubeCoders/AMPTemplates) sehen Vorlagendateien im Repository-Hauptverzeichnis vor.

## Projekt

Entwickelt von **Fabius-Haselnuss** für Life is Feudal: Your Own.
