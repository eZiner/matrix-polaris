# 📑 Projekt POLARIS: Das Matrix-Polaris-Gateway (Teil 1)

**Technische Spezifikation der serverseitigen Geofencing- und PostGIS-Datenbank-Infrastruktur**

**Autor:** eZiner  
**Status:** Technisches Whitepaper (Zielgruppen: Software-Entwickler, System-Administratoren, Netzwerker)  
**Datum:** August 2026  

---

## 1. Die Gateway-Architektur (Backend)

Das *Matrix Polaris Gateway* agiert als intelligenter, asynchroner Vermittler zwischen dem föderierten Matrix-Server-Netzwerk und der geografischen Datenbank [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec]. Die Kernaufgabe besteht darin, eingehende standardisierte Standort-Ereignisse (`m.beacon` oder `m.location`) in Echtzeit zu verarbeiten, ohne den Matrix-Hauptprozess zu blockieren [Matrix Live Location Sharing Spec].

---

## 2. Das relationale Geodaten-Modell (PostgreSQL / PostGIS)

Um Tausende von Gemeinde- und Landkreisgrenzen im laufenden Betrieb dynamisch zu verwalten, verzichtet POLARIS auf fest einprogrammierte Koordinaten (Hardcoding). Die Geofences werden in einem hochperformanten **PostGIS-Cluster** abgelegt.

### Datenbank-Initialisierung und Tabellen-Struktur:
Die Speicherung der Zonen erfolgt als mathematische Polygone im globalen GPS-Standardkoordinatensystem **WGS84** (repräsentiert durch die SRID 4326). Zur massiven Beschleunigung der räumlichen Berechnungen wird ein **Räumlicher Index (GiST)** auf die Geometrie-Spalte gelegt:

```sql
-- 1. Aktivierung der Geodaten-Erweiterung PostGIS
CREATE EXTENSION IF NOT EXISTS postgis;

-- 2. Struktur des föderierten Zonen-Registers
CREATE TABLE polaris_zones (
    id SERIAL PRIMARY KEY,
    zone_name VARCHAR(100) NOT NULL,
    matrix_room_id VARCHAR(100) NOT NULL,
    geom GEOMETRY(Polygon, 4326) NOT NULL
);

-- 3. Spatial Index für echtzeitfähige Abfragen im Pendlerverkehr
CREATE INDEX idx_polaris_zones_geom ON polaris_zones USING GIST(geom);
```
---

## 3. Asynchrone SQL Spatial Queries (Echtzeit-Verarbeitung)

Die `bot.py` empfängt die vom Element- oder Neo-Client via Matrix-Föderation weitergeleiteten GPS-Koordinaten [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec]. Anstatt die Geometrie auf Anwendungsebene rechenintensiv zu parsen, delegiert das Gateway die Abfrage über den asynchronen Treiber `asyncpg` per Bounding-Box-Prüfung direkt an die Datenbank:

```sql
SELECT zone_name, matrix_room_id 
FROM polaris_zones 
WHERE ST_Contains(geom, ST_SetSRID(ST_MakePoint($1, $2), 4326));
```

Diese SQL-Abfrage prüft in Millisekunden, in welchem städtischen Polygon sich der Längengrad (`$1`) und Breitengrad (`$2`) des Bürgers befindet [Matrix-based G2X communication („Neo“) - GitLab].

---

## 4. Hysterese- und Cooldown-Algorithmus (Ping-Pong-Schutz)

Um im dichten Berufsverkehr oder bei ungenauem GPS-Empfang (z.B. in waldreichen Regionen wie dem Harz) kaskadierende Server-Lasten zu verhindern, implementiert das Gateway eine zeitbasierte Dämpfung logischer Zustände.

```text
[ Bürger verlässt Zone ] ➔ Auf Warteliste setzen (`EXIT_PENDING_USERS`) ➔ Cooldown startet

                                 |                                           |
                    (Rückkehr innerhalb von 10 Min?)                  (Zeit abgelaufen?)

                                 |                                           |
                                 v                                           v
                     [ Kick abgebrochen: Bleibt im Raum ]           [ Echter Exit: m.room.kick ]
```

### Die Ablauflogik des Schutzes:
* **Der Eintritt (Enter):** Befindet sich der Bürger laut PostGIS-Abfrage in einer neuen Zone, wird sofort eine Einladung an das Nutzerkonto abgesetzt [Matrix-based G2X communication („Neo“) - GitLab]. Befand sich die Raum-ID zuvor auf der temporären Warteliste, wird sie dort sofort gelöscht (Rettung aus dem Cooldown).
* **Die Warteliste (Exit Pending):** Verlässt das Signal das Polygon, wird der Nutzer **nicht** sofort gekickt [Matrix Live Location Sharing Spec]. Das Gateway speichert den aktuellen Zeitstempel im internen Register `EXIT_PENDING_USERS` ab.
* **Der asynchrone Cleanup-Task:** Eine permanente Hintergrundschleife (`cooldown_cleanup_loop`) prüft sekündlich alle Einträge auf der Warteliste. Erst wenn die Differenz zwischen der aktuellen Serverzeit und dem Exit-Zeitstempel die definierte Pufferzeit (z. B. 600 Sekunden für 10 Minuten) überschreitet, wird der Befehl `m.room.kick` ausgeführt und das Endgerät des Bürgers datenschutzkonform bereinigt [Matrix Live Location Sharing Spec].
