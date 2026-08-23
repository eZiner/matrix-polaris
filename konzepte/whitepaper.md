# 📑 Whitepaper: Projekt POLARIS

[Auf Deutsch lesen](#de-de-german) | [Read in English](#en-us-english)

---

## DE-DE (German)

**Architektur, Funktionsweise und Roadmap eines bundesweiten, dezentralen Geo-Fencing-Gateways auf Matrix-Basis**

**Autor:** eZiner  
**Status:** Technisches Whitepaper (Entwurfsstadium)  
**Datum:** August 2026  

---

### 1. Management Key-Facts
Projekt POLARIS (Matrix Polaris Gateway) definiert den digitalen Bürger-Raum neu. Durch die intelligente Verknüpfung des dezentralen, föderierten Kommunikationsprotokolls **Matrix** mit moderner **Geofencing-Technologie** entsteht eine unkommerzielle, datenschutzkonforme Infrastruktur für Kommunen, Universitäten und Bürger [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec]. 

Das System arbeitet nach dem Prinzip **„Smart Server, Dumb Client“**: Der Bürger nutzt bestehende Open-Source-Messenger (wie *Element*) [Matrix-based G2X communication („Neo“) - GitLab]. Das System erkennt bei Bewegung (z. B. Autofahrt, ÖPNV, Fahrrad) berührungslos den Wechsel geografischer Zonen, lädt den Nutzer vollautomatisch in regionale Behörden- oder Tourist-Infokanäle ein und bereinigt die App beim Verlassen der Zone durch einen automatisierten Austritt (Kick) [Matrix Live Location Sharing Spec].

---

### 2. Zielarchitektur & Kernkomponenten

Im Endausbau verlässt das System den Zustand fester Programmiercodes (Hardcoding) und wechselt in eine hochskalierbare, dezentrale Microservice-Architektur.

```text
+--------------------------------------------------------------+

|                   Bürger-Client (Element)                    |
+--------------------------------------------------------------+
         | (m.location / m.beacon via Föderation)
         v
+--------------------------------------------------------------+

|                 POLARIS-Gateway (Rust-Core)                  |
+--------------------------------------------------------------+
         | (Räumliche SQL-Abfrage / REST-API)
         v
+--------------------------------------------------------------+

|       PostgreSQL / PostGIS-Datenbank (Zonen-Register)        |
+--------------------------------------------------------------+
```

*   **Der Core-Dienst (POLARIS Engine):** Der aktuelle Python-Prototyp wird im Zielzustand in **Rust (Matrix-Rust-SDK)** überführt. Dies garantiert minimale Latenzzeiten und maximale Thread-Sicherheit bei massenhaften Zonenwechseln im Berufsverkehr.
*   **Das föderierte Zonen-Register (PostGIS):** Die geografischen Grenzen werden als mathematische Polygone in einer **PostgreSQL-Datenbank mit PostGIS-Erweiterung** verwaltet. Jedes Polygon repräsentiert eine Zuständigkeitszelle (Gemeinde, Landkreis, Nationalpark) und hält n-Verknüpfungen zu verschlüsselten Matrix-Raum-IDs.
*   **Das Postersatz-Modell im Bürgerbüro:** Um die Hürden der BundID (NFC-Zwang, Elster-Zertifikate) zu umgehen, fungiert das physische Bürgerbüro als **Vertrauensanker**. Nach analoger Identitätsprüfung wird ein anonymer Krypto-Token generiert, der dem Bürger sofort seinen lebenslangen, sicheren Matrix-Account freischaltet.

---

### 3. Detaillierter Funktionsablauf

1.  **Detektion:** Das Smartphone des Bürgers sendet im Hintergrund standardisierte `m.beacon`-Events an seinen Heim-Server [Matrix Live Location Sharing Spec]. Das POLARIS Gateway fängt dieses verschlüsselte Signal ab.
2.  **Räumliche Filterung (Spatial Query):** Die Koordinaten werden mittels einer indizierten PostGIS-Abfrage abgeglichen:
    ```sql
    SELECT room_id, zone_name FROM polaris_zones 
    WHERE ST_Contains(geom, ST_SetSRID(ST_MakePoint(%lon, %lat), 4326));
    ```
3.  **Föderierter Beitritt:** Das System ermittelt instantan die Schnittmenge aller gültigen Räume. Über das Matrix-Föderationsprotokoll wird eine Einladung an das Heimatkonto des Nutzers gesendet und im Hintergrund per Auto-Join vollzogen.
4.  **Das datenschutzkonforme Exit-Protokoll:** Sobald aufeinanderfolgende Signale zeigen, dass der Nutzer die Zone dauerhaft verlassen hat, setzt der Bot einen `m.room.kick`-Befehl ab, um Datenhygiene auf dem Endgerät zu garantieren.

---

### 4. Roadmap: Noch zu implementierende Funktionen

*   **🛠️ Modul 1: Dynamische PostGIS-Datenbankanbindung**
    *   *Ziel:* Ersatz der festen Polygone durch eine asynchrone Datenbank-Verbindung (`asyncpg`). Dadurch wird das Hinzufügen neuer Städte im laufenden Betrieb ohne Server-Neustart möglich.
*   **🛠️ Modul 2: Hysterese- und Karenzzeit-Algorithmus (Ping-Pong-Schutz)**
    *   *Ziel:* Ein *Exit* wird erst vollzogen, wenn der Nutzer sich entweder mehr als X Kilometer aus der Zone entfernt hat ODER für einen Zeitraum von mindestens 10 Minuten (Time-to-Live) kein Signal mehr innerhalb der Zone registriert wurde. Das verhindert Fehlauslösungen an Grenzen.
*   **🛠️ Modul 3: Filterung nach Raum-Kategorien (Scopes)**
    *   *Ziel:* Der Bürger steuert über Chat-Befehle, welche Kanäle er empfangen möchte: *Scope 1: Katastrophenschutz/Verwaltung* (Immer aktiv), *Scope 2: Tourismus/Kultur* (Optional), *Scope 3: Lokaler Handel/Gewerbe* (Opt-in).

---

## EN-US (English)

**Architecture, Functionality, and Roadmap of a Nationwide, Decentralized Matrix-Based Geo-Fencing Gateway**

---

### 1. Executive Summary
Project POLARIS (Matrix Polaris Gateway) redefines the digital civic space. By intelligently linking the decentralized, federated **Matrix communication protocol** with modern **geofencing technology**, it establishes a non-commercial, privacy-compliant public infrastructure for municipalities, universities, and citizens [Matrix-based G2X communication („Neo“) - GitLab, Matrix Live Location Sharing Spec].

The system follows a **"Smart Server, Dumb Client"** architecture: citizens utilize existing open-source messengers (such as *Element*) [Matrix-based G2X communication („Neo“) - GitLab]. The system detects seamless geographical zone transitions during transit (e.g., car rides, public transit, cycling), automatically invites the user to local administrative or tourism info channels, and purges the app upon departure using an automated room kick [Matrix Live Location Sharing Spec].

---

### 2. Target Architecture & Core Components

In its final evolutionary state, the system transitions from hardcoded values into a highly scalable, decentralized microservice architecture.

```text
+--------------------------------------------------------------+

|                    Citizen Client (Element)                  |
+--------------------------------------------------------------+
         | (m.location / m.beacon via Federation)
         v
+--------------------------------------------------------------+

|                 POLARIS Gateway (Rust Core)                  |
+--------------------------------------------------------------+
         | (Spatial SQL Query / REST API)
         v
+--------------------------------------------------------------+

|       PostgreSQL / PostGIS Database (Zone Register)          |
+--------------------------------------------------------------+
```

*   **The Core Service (POLARIS Engine):** The current Python prototype will be ported to **Rust (Matrix-Rust-SDK)** for production. This guarantees minimal latency and optimal thread safety during massive rush-hour zone transitions.
*   **The Federated Zone Register (PostGIS):** Geographical boundaries are managed as mathematical polygons within a **PostgreSQL database with PostGIS extensions**. Each polygon represents an administrative cell (municipality, county, national park) and holds n-mappings to encrypted Matrix room IDs.
*   **The Trust Anchor Model:** To bypass the high friction of federal identity card infrastructure (NFC smartphone scanning, certificates), the physical municipal citizens' office acts as a **Trust Anchor**. After physical verification, an anonymous crypto-token is generated, immediately activating the citizen's lifelong, secure Matrix account.

---

### 3. Detailed Functional Workflow

1.  **Detection:** The citizen's smartphone broadcasts standardized `m.beacon` events to its home server in the background [Matrix Live Location Sharing Spec]. The POLARIS Gateway intercepts this encrypted signal.
2.  **Spatial Query:** The coordinates are matched via an indexed PostGIS database query:
    ```sql
    SELECT room_id, zone_name FROM polaris_zones 
    WHERE ST_Contains(geom, ST_SetSRID(ST_MakePoint(%lon, %lat), 4326));
    ```
3.  **Federated Join:** The system instantly calculates the intersection of all valid rooms. Using the Matrix federation protocol, an invite is transmitted to the user's home account and processed in the background via auto-join.
4.  **Privacy-Compliant Exit Protocol:** Once sequential signals verify that the user has permanently left the zone, the bot executes an `m.room.kick` command to guarantee data hygiene on the user's device.

---

### 4. Roadmap: Pending Implementations

*   **🛠️ Module 1: Dynamic PostGIS Database Integration**
    *   *Goal:* Replacing memory-bound polygons with an asynchronous database connection (`asyncpg`). This allows municipalities to register new boundaries on-the-fly without restarting the service core.
*   **🛠️ Module 2: Hysteresis & Grace-Period Algorithm (Ping-Pong Protection)**
    *   *Goal:* Exits will only be finalized if the user moves more than X kilometers away from the boundary OR if no location signals are registered within the zone for a minimum Time-to-Live (TTL) of 10 minutes. This eliminates boundary jitter.
*   **🛠️ Module 3: Room Category Filtering (Scopes)**
    *   *Goal:* Citizens can filter incoming dynamic channels via chat commands: *Scope 1: Emergency/Administration* (Always active), *Scope 2: Tourism/Culture* (Optional), *Scope 3: Local Commerce/Gewerbe* (Opt-in).

---

