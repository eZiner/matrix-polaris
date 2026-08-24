# 📑 Whitepaper: Projekt POLARIS

[Auf Deutsch lesen](#de-de-german) | [Read in English](#en-us-english)

---

## DE-DE (German)

**Architektur, Funktionsweise und Roadmap eines bundesweiten, dezentralen Geo-Fencing-Gateways auf Matrix-Basis unter Verzicht auf die BundID-Infrastruktur**

**Autor:** eZiner  
**Status:** Technisches Whitepaper (Erweitert um FITKO Neo-Konformität)  
**Datum:** August 2026  

---

### 1. Management Key-Facts
Projekt POLARIS (Matrix Polaris Gateway) definiert den digitalen Bürger-Raum neu. Durch die intelligente Verknüpfung des dezentralen, föderierten Kommunikationsprotokolls **Matrix** mit moderner **Geofencing-Technologie** entsteht eine unkommerzielle, datenschutzkonforme Infrastruktur für Kommunen, Universitäten und Bürger [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec]. 

Das System arbeitet nach dem Prinzip **„Smart Server, Dumb Client“**: Der Bürger nutzt offene Messenger-Schnittstellen (wie die der FITKO-Initiative „Neo“ oder Standard-Clients wie *Element*) [Matrix-based G2X communication („Neo“) - GitLab]. Das System erkennt bei Bewegung berührungslos den Wechsel geografischer Zonen, lädt den Nutzer vollautomatisch in regionale Behörden- oder Tourist-Infokanäle ein und bereinigt die App beim Verlassen der Zone durch einen automatisierten Austritt (Kick) [Matrix Live Location Sharing Spec].

---

### 2. Zielarchitektur & Datenbank-Infrastruktur

Im Endausbau verlässt das System den Zustand fester Programmiercodes (Hardcoding). Die Geofences werden vollständig in eine hochperformante relationale Geodatenbank ausgelagert.

```text
+--------------------------------------------------------------+

|            Bürger-Client (Neo-Frontend / Element)            |
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
Die geografischen Grenzen Deutschlands (Gemeinden, Landkreise) werden als mathematische Polygone mit dem globalen GPS-Koordinatensystem WGS84 (SRID 4326) im kommunalen Rechenzentrum verwaltet [Matrix Live Location Sharing Spec]. 

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
3.  **Föderierter Beitritt & Hysterese:** Das System ermittelt die Schnittmenge aller gültigen Räume. Über das Matrix-Föderationsprotokoll wird eine Einladung an das Heimatkonto des Nutzers gesendet [Matrix-based G2X communication („Neo“) - GitLab].
4.  **Das datenschutzkonforme Exit-Protokoll:** Sobald aufeinanderfolgende Signale zeigen, dass der Nutzer die Zone verlässt, greift ein zeitbasierter Hysterese-Schutz (`EXIT_PENDING_USERS`), bevor nach Ablauf der Karenzzeit der endgültige `m.room.kick`-Befehl abgesetzt wird [Matrix Live Location Sharing Spec].

---

### 4. Die BundID-Alternative: Souveräne Kommunal-Homeserver (FITKO Neo-Interoperabilität)

Ein zentraler Grundpfeiler von Projekt POLARIS ist der **vollständige Verzicht auf die staatliche BundID-Infrastruktur** (NFC-Zwang, Elster-Zertifikate, zentrale Kontenregister). Um dieses Ziel zu erreichen und dennoch das moderne, offizielle Benutzerinterface des FITKO-Projekts „Neo“ zu nutzen, ist **kein Code-Fork** nötig. POLARIS nutzt stattdessen die offene Schnittstellen-Architektur des Matrix-Standards [Matrix-based G2X communication („Neo“) - GitLab].

```text
[ FITKO Neo-Apps / Web-Frontend ] ➔ (Standard Matrix API)
                                           |
                                           v
                 [ Kommunaler Homeserver (z.B. matrix.duesseldorf.de) ]
                                           ^
                                           | (Lokale Nutzerauthentifizierung)
                            [ POLARIS Postersatz-Modell ]
```

#### Das Schnittstellen-Prinzip statt Fork-Aufwand
Das Bundesprojekt Neo besteht im Kern aus Open-Source-Frontends (Web-App, iOS- und Android-Clients), die über die standardisierte Matrix-Client-Server-API mit einem Server kommunizieren [Matrix-based G2X communication („Neo“) - GitLab]. Die BundID ist darin lediglich als ein austauschbares Login-Modul (Identity Provider) angehängt. 

POLARIS kappt diese Schnittstelle zur BundID auf Serverebene und ersetzt sie durch das **Postersatz-Modell**:
*   **Kommunale Datenhoheit:** Jede Kommune betreibt einen eigenen, souveränen Matrix-Homeserver (z. B. `matrix.duesseldorf.de`) [Matrix-based G2X communication („Neo“) - GitLab].
*   **Physischer Vertrauensanker:** Die Verifizierung und Erstellung der Bürger-Accounts erfolgt direkt und datenschutzkonform bei der analogen Identitätsprüfung im lokalen Bürgerbüro. Es wird ein kryptografischer Token erzeugt.
*   **Nahtlose UI-Kompatibilität:** Der Bürger kann die offiziellen, barrierefreien Neo-Frontends der FITKO uneingeschränkt nutzen [Matrix-based G2X communication („Neo“) - GitLab]. Beim Login verbindet sich die App jedoch nicht mit einer Bundes-Zentraldatenbank, sondern authentifiziert sich direkt am lokalen, kommunalen Homeserver über den im Bürgerbüro erhaltenen Token.

#### 4.1 Rechtliche Rahmenbedingungen und freie Nachnutzung (Public Code)
Da das Projekt Neo der FITKO unter einer freien Open-Source-Lizenz im offiziellen OpenCoDE-Repository des Bundes bereitgestellt wird, ist für die Integration in POLARIS **kein Code-Fork** erforderlich. Die Nachnutzung und Anpassung der Benutzeroberfläche erfolgt im Rahmen der Open-Source-Vorgaben der öffentlichen Hand („Public Money? Public Code!“). Es werden lediglich die standardisierten Client-Schnittstellen (APIs) bedient, um die Neo-Userinterfaces direkt an die dezentralen, kommunalen POLARIS-Homeserver anzubinden [openDesk Portal, Matrix-based G2X communication („Neo“) - GitLab].

---

### 5. Roadmap: Technische Meilensteine

*   **[x] Modul 2: Asynchroner Hysterese-Schutz (Ping-Pong-Schutz)**
    *   *Status:* **Implementiert.** Zeitbasierte Warteliste fängt Signalsprünge an Regionsgrenzen ab.
*   **[ ] Modul 1: Dynamische PostGIS-Datenbankanbindung**
    *   *Status:* **Konzipiert.** Die Migration der lokalen Shapely-Polygone in die oben definierte PostGIS-Struktur ermöglicht das Aufschalten neuer Städte im laufenden Betrieb.
*   **[ ] Modul 3: Kategorien-Filter (Scopes)**
    *   *Status:* **In Planung.** Ermöglicht dem Bürger, via Chat-Befehl (z. B. `!scope tourismus aus`) optionale Info-Kanäle stummzuschalten oder zu blockieren.

---

## EN-US (English)

**Architecture, Functionality, and Roadmap of a Nationwide, Decentralized Matrix-Based Geo-Fencing Gateway without BundID Dependencies**

---

### 1. Executive Summary
Project POLARIS (Matrix Polaris Gateway) redefines the digital civic space. By intelligently linking the decentralized, federated **Matrix communication protocol** with modern **geofencing technology**, it establishes a non-commercial, privacy-compliant public infrastructure for municipalities, universities, and citizens [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec].

The system follows a **"Smart Server, Dumb Client"** architecture: citizens utilize open messenger interfaces (such as the FITKO "Neo" frontends or standard clients like *Element*) [Matrix-based G2X communication („Neo“) - GitLab]. The system detects seamless geographical zone transitions during transit, automatically invites the user to local administrative or tourism info channels, and purges the app upon departure using an automated room kick [Matrix Live Location Sharing Spec].

---

### 2. Target Architecture & Database Infrastructure

In its final evolutionary state, the system shifts away from hardcoded values. Geofences are completely decoupled into a highly performant relational spatial database.

```text
+--------------------------------------------------------------+

|            Citizen Client (Neo-Frontend / Element)           |
+--------------------------------------------------------------+
         | (m.location / m.beacon via Federation)
         v
+--------------------------------------------------------------+

