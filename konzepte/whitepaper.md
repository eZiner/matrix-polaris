# 📑 Whitepaper: Projekt POLARIS

[Auf Deutsch lesen](#de-de-german) | [Read in English](#en-us-english)

---

## DE-DE (German)

**Architektur, Funktionsweise und Roadmap eines bundesweiten, dezentralen Geo-Fencing-Gateways auf Matrix-Basis**

**Autor:** eZiner  
**Status:** Technisches Whitepaper (Erweitert um PostGIS-Spezifikation)  
**Datum:** August 2026  

---

### 1. Management Key-Facts
Projekt POLARIS (Matrix Polaris Gateway) definiert den digitalen Bürger-Raum neu. Durch die intelligente Verknüpfung des dezentralen, föderierten Kommunikationsprotokolls **Matrix** mit moderner **Geofencing-Technologie** entsteht eine unkommerzielle, datenschutzkonforme Infrastruktur für Kommunen, Universitäten und Bürger [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec]. 

Das System arbeitet nach dem Prinzip **„Smart Server, Dumb Client“**: Der Bürger nutzt bestehende Open-Source-Messenger (wie *Element*) [Matrix-based G2X communication („Neo“) - GitLab]. Das System erkennt bei Bewegung (z. B. Autofahrt, ÖPNV, Fahrrad) berührungslos den Wechsel geografischer Zonen, lädt den Nutzer vollautomatisch in regionale Behörden- oder Tourist-Infokanäle ein und bereinigt die App beim Verlassen der Zone durch einen automatisierten Austritt (Kick) [Matrix Live Location Sharing Spec].

---

### 2. Zielarchitektur & Datenbank-Infrastruktur

Im Endausbau verlässt das System den Zustand fester Programmiercodes (Hardcoding). Die Geofences werden vollständig in eine hochperformante relationale Geodatenbank ausgelagert.

```text
+--------------------------------------------------------------+

|                   Bürger-Client (Element)                    |
+--------------------------------------------------------------+
         | (m.location / m.beacon via Föderation)
         v
+--------------------------------------------------------------+

|             POLARIS-Gateway (Python / Rust-Core)             |
+--------------------------------------------------------------+
         | (Asynchrone SQL Spatial Query via asyncpg)
         v
+--------------------------------------------------------------+

|       PostgreSQL / PostGIS-Datenbank (Zonen-Register)        |
+--------------------------------------------------------------+
```

#### Das relationale Geodaten-Modell (PostGIS)
Die geografischen Grenzen Deutschlands (Gemeinden, Landkreise) werden als mathematische Polygone mit dem globalen GPS-Koordinatensystem WGS84 (SRID 4326) im kommunalen Rechenzentrum verwaltet. 

Um maximale Performance bei tausenden parallelen Standort-Pings im Berufsverkehr zu garantieren, wird die Tabelle über einen **Räumlichen Index (GiST)** datenbankseitig optimiert:

```sql
-- Aktivierung der Geodaten-Erweiterung
CREATE EXTENSION IF NOT EXISTS postgis;

-- Struktur des föderierten Zonen-Registers
CREATE TABLE polaris_zones (
    id SERIAL PRIMARY KEY,
    zone_name VARCHAR(100) NOT NULL,
    matrix_room_id VARCHAR(100) NOT NULL,
    geom GEOMETRY(Polygon, 4326) NOT NULL
);

-- Räumlicher Index für echtzeitfähige ST_Contains-Abfragen
CREATE INDEX idx_polaris_zones_geom ON polaris_zones USING GIST(geom);
```

---

### 3. Detaillierter Funktionsablauf

1.  **Detektion:** Das Smartphone des Bürgers sendet im Hintergrund standardisierte `m.beacon`-Events an seinen Heim-Server [Matrix Live Location Sharing Spec]. Das POLARIS Gateway fängt dieses verschlüsselte Signal ab.
2.  **Räumliche Filterung (Spatial Query):** Das Gateway berechnet die Flächenzugehörigkeit nicht auf Anwendungsebene, sondern delegiert die mathematische Prüfung per asynchronem Datenbank-Treiber (`asyncpg`) direkt an den PostGIS-Kern:
    ```sql
    SELECT zone_name, matrix_room_id 
    FROM polaris_zones 
    WHERE ST_Contains(geom, ST_SetSRID(ST_MakePoint($1, $2), 4326));
    ```
3.  **Föderierter Beitritt & Hysterese:** Das System ermittelt die Schnittmenge aller gültigen Räume. Über das Matrix-Föderationsprotokoll wird eine Einladung an das Heimatkonto des Nutzers gesendet.
4.  **Das datenschutzkonforme Exit-Protokoll:** Sobald aufeinanderfolgende Signale zeigen, dass der Nutzer die Zone verlässt, greift ein zeitbasierter Hysterese-Schutz (`EXIT_PENDING_USERS`), bevor nach Ablauf der Karenzzeit der endgültige `m.room.kick`-Befehl abgesetzt wird.

---

### 4. Roadmap: Technische Meilensteine

*   **[x] Modul 2: Asynchroner Hysterese-Schutz (Ping-Pong-Schutz)**
    *   *Status:* **Implementiert.** Zeitbasierte Warteliste fängt Signalsprünge an Regionsgrenzen ab.
*   **[ ] Modul 1: Dynamische PostGIS-Datenbankanbindung**
    *   *Status:* **Konzipiert.** Die Migration der lokalen Shapely-Polygone in die oben definierte PostGIS-Struktur ermöglicht das Aufschalten neuer Städte im laufenden Betrieb.
*   **[ ] Modul 3: Kategorien-Filter (Scopes)**
    *   *Status:* **In Planung.** Ermöglicht dem Bürger, via Chat-Befehl (z. B. `!scope tourismus aus`) optionale Info-Kanäle stummzuschalten oder zu blockieren.

---

## EN-US (English)

**Architecture, Functionality, and Roadmap of a Nationwide, Decentralized Matrix-Based Geo-Fencing Gateway**

---

### 1. Executive Summary
Project POLARIS (Matrix Polaris Gateway) redefines the digital civic space. By intelligently linking the decentralized, federated **Matrix communication protocol** with modern **geofencing technology**, it establishes a non-commercial, privacy-compliant public infrastructure for municipalities, universities, and citizens [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec].

The system follows a **"Smart Server, Dumb Client"** architecture: citizens utilize existing open-source messengers (such as *Element*) [Matrix-based G2X communication („Neo“) - GitLab]. The system detects seamless geographical zone transitions during transit (e.g., car rides, public transit, cycling), automatically invites the user to local administrative or tourism info channels, and purges the app upon departure using an automated room kick [Matrix Live Location Sharing Spec].

---

### 2. Target Architecture & Database Infrastructure

In its final evolutionary state, the system shifts away from hardcoded values. Geofences are completely decoupled into a highly performant relational spatial database.

```text
+--------------------------------------------------------------+

|                    Citizen Client (Element)                  |
+--------------------------------------------------------------+
         | (m.location / m.beacon via Federation)
         v
+--------------------------------------------------------------+

|             POLARIS Gateway (Python / Rust Core)             |
+--------------------------------------------------------------+
         | (Asynchronous SQL Spatial Query via asyncpg)
         v
+--------------------------------------------------------------+

|       PostgreSQL / PostGIS Database (Zone Register)          |
+--------------------------------------------------------------+
```

#### The Spatial Relational Data Model (PostGIS)
Geographical boundaries of municipalities are managed as mathematical polygons using the WGS84 coordinate system (SRID 4326) within municipal data centers.

To ensure massive scalability during peak commuting hours, a **Spatial Index (GiST)** optimized database-side processing is implemented:

```sql
CREATE EXTENSION IF NOT EXISTS postgis;

CREATE TABLE polaris_zones (
    id SERIAL PRIMARY KEY,
    zone_name VARCHAR(100) NOT NULL,
    matrix_room_id VARCHAR(100) NOT NULL,
    geom GEOMETRY(Polygon, 4326) NOT NULL
);

CREATE INDEX idx_polaris_zones_geom ON polaris_zones USING GIST(geom);
```

---

### 3. Detailed Functional Workflow

1.  **Detection:** The citizen's smartphone broadcasts standardized `m.beacon` events to its home server [Matrix Live Location Sharing Spec]. The POLARIS Gateway intercepts this encrypted signal.
2.  **Spatial Query:** Geometry calculation happens directly inside the database cluster via asynchronous drivers (`asyncpg`), using an indexed bounding-box query:
    ```sql
    SELECT zone_name, matrix_room_id 
    FROM polaris_zones 
    WHERE ST_Contains(geom, ST_SetSRID(ST_MakePoint($1, $2), 4326));
    ```
3.  **Federated Join & Hysteresis:** The system resolves the room matches. Using the Matrix federation protocol, an invite is transmitted to the user's home account.
4.  **Privacy-Compliant Exit Protocol:** When location logs indicate a user has left a zone, a time-based hysteresis routine (`EXIT_PENDING_USERS`) cushions the connection before the final `m.room.kick` command is fired.

---

### 4. Roadmap: Technical Milestones

*   **[x] Module 2: Asynchronous Hysteresis Protection (Ping-Pong Protection)**
    *   *Status:* **Implemented.** Protects boundaries from signal jitter.
*   **[ ] Module 1: Dynamic PostGIS Database Integration**
    *   *Status:* **Designed.** Moving local geometric data structures to PostGIS to allow dynamic boundary registrations without server restarts.
*   **[ ] Module 3: Room Category Filtering (Scopes)**
    *   *Status:* **Planned.** Allows filtering dynamic channels via custom bot commands (e.g., `!scope tourism off`).

---

## 📄 License & Trademark Disclaimer

This project is licensed under the **MIT License**. Feel free to use, copy, and modify this concept for your own municipality or research project.

⚠️ **Rechtlicher Hinweis / Trademark Disclaimer:**  
