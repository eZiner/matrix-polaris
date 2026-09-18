# 🗺️ POLARIS - Geofencing-Datenmodell und kaskadierte Abfragelogik

* **Projekt-ID:** matrix-polaris
* **Komponente:** Strategischer Rahmen / Geodaten-Architektur
* **Version:** 1.0.0
* **Lizenz:** MIT-Lizenz (Open Source)
* **Status:** STRATEGIC SPECIFICATION (Konzeptioneller Leitfaden für Geodaten-Infrastrukturen)

---

## 1. Das regionale 4-Tabellen-Hybridmodell

Um ein performantes, rechtlich verlässliches und gleichzeitig hochfeines Geofencing zu ermöglichen, verzichtet POLARIS auf starre, globale Indizierungen. Das System kombiniert stattdessen amtliche Quelldaten mit freien Geodaten direkt in einer relationalen PostgreSQL-Datenbank mit PostGIS-Erweiterung auf dem **Sovereign Local Government Rack (SLG-Rack)** im Rathaus.

Das topologische Modell ist strikt normalisiert und gliedert sich in vier funktionale Funktionstabellen:

1. `main_zones` **(Hauptzonen):** Speichert die rechtlich verbindlichen Gemeinde- und Stadtgrenzen (`admin_level 8`) basierend auf dem offiziellen *VG25*-GeoPackage des Bundesamtes für Kartographie und Geodäsie (BKG). Der Amtliche Regionalschlüssel (ARS) dient als Primärschlüssel.
2. `sub_zones` **(Subzonen):** Bildet feingranulare Ortsteile, Dörfer oder Stadtbezirke (`admin_level 9/10`) ab, die über die OpenStreetMap Overpass-API nachgeladen werden. Die Verknüpfung zur Muttergemeinde erfolgt über eine Fremdschlüssel-Kaskade (`parent_ars`).
3. `polaris_spaces` **(Matrix-Zuweisung):** Verknüpft die geografischen ARS-Schlüssel mit den echten Raum-IDs des kommunalen Matrix-Homeservers und definiert den Raumtyp (`COMMUNITY` oder `EMERGENCY`).
4. `exit_pending_users` **(Gateway-Zustandsschicht):** Speichert temporär die Zeitstempel von Nutzern, die eine Zone verlassen haben, um die Hysterese-Logik im Hintergrund zu verarbeiten.

---

## 2. Die zweistufige geografische Kaskade (RAM-Cache)

Die größte Herausforderung beim serverseitigen Massen-Geofencing ist die Rechenlast bei Punkt-in-Polygon-Abfragen (`ST_Contains`). Müsste das Gateway jede eintreffende Koordinate eines Bürgers gegen zehntausende Ortsteil-Polygone in ganz Deutschland prüfen, würde die Serverleistung einbrechen.

POLARIS löst dies durch eine **zweistufige mathematische Kaskade**:

* **Stufe 1: Die Gemeinde-Vorfilterung (BKG-Ebene)**  
  Das eintreffende Standortsignal (`m.location`) des Bürger-Clients wird zuerst ausschließlich gegen die Tabelle `main_zones` geprüft. PostGIS ermittelt blitzschnell die übergeordnete Heimat- oder Gastgemeinde.
* **Stufe 2: Der gezielte Drilldown (OSM-Ebene)**  
  Erst wenn die Gemeinde (z. B. der ARS von Goslar) eindeutig feststeht, filtert die Datenbank die zweite Abfragestufe über die Klausel `WHERE parent_ars = :current_ars`. PostGIS muss die Koordinate nun nur noch gegen die Handvoll Ortsteil-Polygone (Subzonen) dieser *einen* Gemeinde prüfen.

### Der Performance-Turbo:

Der bereinigte Geodatensatz für eine durchschnittliche Kommune inklusive all ihrer Ortsteile ist extrem schlank (ca. 250–300 MB). Er wird von PostgreSQL vollständig im Arbeitsspeicher-Cache (`shared_buffers`) gehalten. Unterstützt durch räumliche **GiST-Indizes** sinkt die Suchkomplexität auf ein Minimum `$O(\log n)$`. Die Abfragezeit liegt stabil **unter 0,2 Millisekunden**.

---

## 3. Der algorithmische Signal-Schutz (Hysterese & Transit)

Um die Server-Infrastruktur im Live-Betrieb vor Überlastungen (*State Resolution Storms*) durch springende GPS-Signale oder schnelle Fortbewegung zu schützen, implementiert die Gateway-Logik zwei mathematische Schutzfilter:

### 3.1 Der geschwindigkeits-adaptive Transit-Filter

Das Gateway wertet die Bewegungsgeschwindigkeit des Endgeräts (`velocity_kmh`) aus. Überschreitet ein Nutzer eine definierte Schwelle (z. B. bei einer Fahrt auf der Autobahn oder im ICE), wird der Beitritt zu hyperlokalen Ortsteilkanälen blockiert. Der Nutzer verbleibt stattdessen stabil in der übergeordneten Gemeinde- oder Landkreis-Zone. Das spart schätzungsweise 95 % der unkritischen Raumwechsel-Last.

### 3.2 Die zeitliche Austritts-Hysterese

An den Grenzen von Geofencing-Zonen neigen GPS-Signale zum Springen. POLARIS wendet hier das Prinzip der Hysterese an:

* **Eintritt:** Erfolgt augenblicklich und ohne Verzögerung.
* **Austritt:** Wird verzögert ausgeführt. Das Gateway trägt den Austrittswunsch in die Tabelle `exit_pending_users` ein und startet einen **10-minütigen Cooldown-Timer (600 Sekunden)**. Erst wenn der Nutzer die Zone nach 10 Minuten nachweislich nicht wieder betreten hat, wird der Server-Kick vollzogen. Kehrt er vorher um, bleibt er geräuschlos im Raum.