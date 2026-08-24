# 📑 Projekt POLARIS: Strategische Projektübersicht

**Dezentrale, föderierte Geo-Fencing-Infrastruktur für die digitale Daseinsvorsorge**

**Autor:** eZiner  
**Status:** Konzeptpapier (Zielgruppen: Entscheidungsträger, Stadtrat, IT-Leitung)  
**Datum:** August 2026  

---

## 1. Ausgangslage und Problemstellung

Die Digitalisierung der deutschen Verwaltung (OZG) leidet unter zwei fundamentalen Hürden: proprietären App-Silos und der hohen Hürde zentraler Identitätsnachweise (BundID). Bürger müssen für jede Kommune, jede Universität und jeden Einsatzzweck (Kita, Abfall, Tourismus) separate, kommerzielle Anwendungen installieren. Gleichzeitig scheitern mobile Behördenportale im Alltag an der Komplexität mobiler NFC-Ausweis-Scans.

Projekt POLARIS bricht diese Silos auf. Es verknüpft das etablierte, vom Bund geförderte Kommunikationsprotokoll **Matrix** mit serverseitiger **Geofencing-Technologie** zu einer souveränen Infrastruktur.

---

## 2. Das Kernprinzip: „Smart Server, Dumb Client“

POLARIS verlagert die gesamte Intelligenz (Geofencing, Datenbankabfragen) auf die serverseitige Infrastruktur der Kommunen. Der Bürger benötigt keine spezialisierte Zusatzsoftware. 

Ein einziger, quelloffener Standard-Messenger (wie *Element* oder das FITKO-Projekt *Neo*) genügt für das gesamte Spektrum der zivilgesellschaftlichen Kommunikation. Bewegt sich der Bürger durch den physischen Raum, steuert das Server-Gateway den dynamischen Informationsfluss vollautomatisch im Hintergrund.

---

## 3. Die drei Säulen der POLARIS-Topologie

Die Gesamtarchitektur gliedert sich in drei logische, voneinander unabhängige Funktionsebenen:

```text
+-------------------------------------------------------------------+

| 1. FEDERATED CORE LAYER (Kommunale & universitäre Homeserver)      |
+-------------------------------------------------------------------+
                                  ^
                                  | (Matrix-Föderation / Port 8448)
                                  v
+-------------------------------------------------------------------+

| 2. REGIONAL GATEWAY LAYER (POLARIS Gateway & PostGIS-Datenbank)    |
+-------------------------------------------------------------------+
                                  ^
                                  | (Client-Server-API / Port 443)
                                  v
+-------------------------------------------------------------------+

| 3. ENCRYPTED EDGE CLIENTS (Souveräne Bürger-Endpunkte)            |
+-------------------------------------------------------------------+
```

1. **Federated Core Layer:** Kommunen und Universitäten betreiben eigene Matrix-Heimserver. Diese sind über das Föderationsprotokoll zu einem ausfallsicheren Mesh-Netzwerk verbunden. Es existiert kein zentraler Point of Failure.
2. **Regional Gateway Layer:** Lokale Rechenzentren betreiben das POLARIS-Gateway, gekoppelt an eine PostgreSQL/PostGIS-Datenbank. Hier werden die geografischen Zuständigkeitsgrenzen als mathematische Polygone verwaltet.
3. **Encrypted Edge Clients:** Der Bürger nutzt sein bestehendes Matrix-Konto. Das mobile Endgerät sendet im Hintergrund verschlüsselte Standorte an den eigenen Heimserver, welcher die Daten verschlüsselt an das zuständige Regional-Gateway routet.

---

## 4. Strategische Vorteile für Kommunen

* **Absolute Datensouveränität:** Alle Metadaten und Kommunikationsinhalte verbleiben in der Hand öffentlich-rechtlicher Rechenzentren (z. B. HannIT) oder Universitäten. Keine Datenabflüsse an kommerzielle Cloud-Anbieter.
* **Skalierbarkeit ohne Mehrkosten:** Das Netzwerk wächst organisch. Tritt eine neue Gemeinde dem Verbund bei, stellt sie eine eigene PostGIS-Instanz auf. Bestehende Infrastrukturen in anderen Bundesländern werden mit keinem Byte Mehrlast belastet.
* **Volle DSGVO-Konformität durch Datenhygiene:** Standorte werden flüchtig verarbeitet. Verlässt ein Bürger ein Polygon, sorgt das automatisierte Exit-Protokoll für eine restlose Bereinigung der Chat-Kanäle auf dem Endgerät.
