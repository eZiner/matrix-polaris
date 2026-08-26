# 🗺️ Projekt POLARIS: Struktur- und Betriebskonzept

Dieses Dokument beschreibt die organisatorische Struktur, das administrative Berechtigungsmodell und das "Postersatz-Modell" für den bundesweiten Rollout von Projekt POLARIS.

---

## 1. Das "Postersatz-Modell" (Die BundID-Alternative)

Um digitale Souveränität und Barrierefreiheit zu garantieren, verzichtet POLARIS auf den Zwang von NFC-Smartphones, eID-Ausweisen oder komplexen zentralen Konten-Registrierungen im Alltag. Das physische Bürgerbüro vor Ort agiert als **Vertrauensanker**.

```text
[ Bürger im Bürgerbüro ] ➔ (Analoge Ausweiskontrolle) ➔ [ Sachbearbeiter ]
                                                               |
                                                      (Dashboard-Klick)
                                                               v
[ Bürger-Messenger (Element) ] 💻➔ (Aktivierungs-Token) ➔ [ QR-Code / Passwort ]
```

### Der Ablauf im Detail:
1. **Identifikation:** Der Bürger legitimiert sich einmalig analog (z.B. bei der Anmeldung des Wohnsitzes oder der Ausgabe eines neuen Personalausweises) im Bürgerbüro.
2. **Token-Generierung:** Der Sachbearbeiter klickt im städtischen POLARIS-Dashboard auf "Neuen Bürger-Account anlegen". Das System generiert im Hintergrund ein verschlüsseltes Matrix-Konto (z.B. `@max.mustermann:clz.de`) und erzeugt einen Aktivierungs-Token (als QR-Code oder Ausdruck).
3. **Erstanmeldung:** Der Bürger scannt den Code zu Hause mit einem beliebigen Endgerät (PC-Browser oder Smartphone). Er befindet sich sofort in seinem privaten, ende-zu-ende verschlüsselten **1-zu-1-Vorgangsraum** mit der Stadtverwaltung.

---

## 2. Raum-Architektur und Daten-Isolation

POLARIS trennt strikt zwischen **privaten Vorgängen** (Datenschutz-Zone) und **öffentlichen Informationen** (Geofencing-Zone).

```text
                               +-----------------------------+

                               |    Bürger-Account (Handy)   |
                               +-----------------------------+
                                  /                       \
        (Verschlüsselter 1-zu-1 Raum)               (Automatischer Geo-Beitritt)
                                /                           \
                               v                             v
+------------------------------------+         +------------------------------------+

|        Städtisches Fachamt         |         |     Regionale Informationsräume    |
|  - Ende-zu-Ende verschlüsselt      |         |  - Unverschlüsselt / Öffentlich    |
|  - Geodaten bleiben unsichtbar     |         |  - Gesteuert durch Geo-Gateway     |
|  - Reines Dialog-Prinzip           |         |  - Automatischer Kick bei Exit     |
+------------------------------------+         +------------------------------------+
```

### A. Der Vorgangsraum (Amtliche Kommunikation)
* **Rechte (Power Levels):** Bürger (0), Fachamt (100) [Power levels - Matrix Spec].
* **Verschlüsselung:** Strengstes Megolm-Protokoll (`m.megolm.v1.aes-sha256`).
* **Sichtbarkeit:** Privat. Für Crawler und andere Server unsichtbar [Join rules - Matrix Spec].

### B. Die Info-Räume (Geofencing & Tourismus)
* **Rechte (Power Levels):** Bürger (0, nur Leserecht), POLARIS-Gateway (50, Moderator für Kicks/Invites), Stadt-Redaktion (100, Schreibrecht) [Power levels - Matrix Spec].
* **Sichtbarkeit:** Öffentlich innerhalb der Föderation, Beitritt aber nur via Gateway-Einladung [Join rules - Matrix Spec].

---

## 3. Bundeweiter Föderations-Ansatz (Das neue Usenet)

POLARIS ist kein zentrales Datensilo. Jede Universität und jede Kommune betreibt im Endausbau ihren eigenen Matrix-Heimserver (z.B. via `Synapse` oder `Dendrite`). 

* **Der Universitäts-Knoten:** Universitäten stellen die technische Infrastruktur und Rechenleistung. Studenten und Wissenschaftler verwalten die regionalen Geofence-Register.
* **Der Kommunal-Knoten:** Städte verwalten die Raum-Inhalte (Meldungen, Straßensperren, Eventtipps) und stellen den Vertrauensanker im Bürgerbüro.
* **Das globale Netzwerk:** Ein Bürger aus Berlin, der durch den Oberharz fährt, empfängt die Einladungen des Harzer POLARIS-Gateways nahtlos auf seinem Berliner Heimat-Server, ohne ein neues Konto anlegen zu müssen.

---

## 4. Zukünftige Funktions-Roadmap

Für die Weiterentwicklung des technischen Gateways (`matrix-polaris-gateway`) sind folgende Meilensteine definiert:

1. **[ ] Modul 1: PostGIS-Datenbankanbindung:** Migration der In-Memory-Polygone in eine performante Geo-Datenbank zur dynamischen Verwaltung von Tausenden Stadtgrenzen im laufenden Betrieb.
2. **[x] Modul 2: Asynchroner Hysterese-Schutz:** Zeitbasierte Warteliste (`EXIT_PENDING_USERS`), um das Springen von GPS-Signalen an Regionsgrenzen abzufedern (Ping-Pong-Schutz).
3. **[ ] Modul 3: Kategorien-Filter (Scopes):** Ermöglicht dem Bürger, via Chat-Befehl (z.B. `!scope tourismus aus`) optionale Info-Kanäle stummzuschalten oder zu blockieren.
