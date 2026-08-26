# Matrix Polaris Gateway - Technische Dokumentation

Diese Dokumentation beschreibt die datenschutzkonforme Architektur, die Event-Steuerung und den detaillierten Kommunikationsfluss des **Matrix Polaris Gateway** (Geofencing-Infrastruktur).

---

## 1. Systemarchitektur: Die Zwei-Schichten-Trennung

Das Gesamtsystem trennt strikt die überregionale Netzwerkinfrastruktur von der lokalen Anwendungs- und Geofencing-Logik, um maximale Datensouveränität und Skalierbarkeit zu garantieren.

### Federated Core Layer (Das globale Fundament)
* **Protokoll-Basis:** Dezentrales Matrix-Protokoll.
* **Funktion:** Transportiert Nachrichten, verwaltet Identitäten und regelt das kryptografische Schlüsselmanagement.
* **Datenschutz:** Der Core Layer sieht sensible Standortdaten ausschließlich als unlesbaren Chiffre-Text (**Ende-zu-Ende-Verschlüsselung mit dem Megolm-Verfahren**). Der Serverbetreiber kann keine Bewegungsprofile erstellen.

### Regional Gateway Layer (Die lokale Schaltzentrale)
* **Komponente:** Ausführender Service-Bot (`bot.py`).
* **Funktion:** Beheimatet die Geofence-Polygone einer bestimmten Gemeinde oder Region. Der Bot wertet eingehende Standorte aus und steuert autonom die Raumeinladungen für lokale Info-Kanäle.
* **Datenschutz (In-Memory):** Daten werden flüchtig im Arbeitsspeicher dechiffriert, analysiert und nach der Entscheidung (`polygon.contains`) sofort verworfen. Es findet kein Logging oder Datenbank-Persistierung statt.

---

## 2. Der detaillierte Kommunikations- & Eventfluss

Der chronologische Ablauf einer Standort-Verarbeitung gestaltet sich wie folgt:

```
[ Edge Client (Handy) ]           [ Matrix Core Server ]          [ Regional Gateway (Bot) ]
          |                                  |                                 |
          |-- (1) GPS-Ortung lokal ---------|                                 |
          |-- (2) E2EE Megolm Verschlüss. ---|                                 |
          |                                  |                                 |
          |---- PUT /rooms/{id}/send ------->|                                 |
          |     (Verschlüsseltes Event)      |                                 |
          |                                  |---- GET /.../sync (Push) ------>|
          |                                  |     (Liefert verschl. Event)    |
          |                                  |                                 |
          |                                  |                        [In-Memory RAM]
          |                                  |                        A. Dechiffrierung
          |                                  |                        B. shapely Check
          |                                  |                        C. Ergebnis: INSIDE
          |                                  |                                 |
          |                                  |<--- POST /rooms/{id}/invite ----|
          |                                  |    (Bot lädt Bürger ein)        |
          |                                  |                                 |
          |<--- Sync / Push Notification ----|                                 |
          |    (Einladung erscheint)         |                                 |
          |                                  |                                 |
          |---- POST /rooms/{id}/join ------>|                                 |
          |    (Bürger tritt Info-Kanal bei) |                                 |
```

### Die Phasen im Detail:
1. **Standortermittlung & Edge-Verschlüsselung:** Das Smartphone-OS liefert die Koordinaten an den Matrix-Client. Dieser verpackt sie in ein standardisiertes `m.location`- oder `m.beacon`-Event und verschlüsselt den Inhalt, bevor er das Gerät verlässt.
2. **Server-Relais:** Der Heimat-Server nimmt das Event (`PUT /rooms/{room_id}/send/m.room.encrypted`) entgegen und leitet es über die Sync-API (`GET /_matrix/client/v3/sync`) an alle autorisierten Raummitglieder – darunter der Bot – weiter.
3. **Flüchtige Geometrie-Prüfung:** Der Bot empfängt das Event, entschlüsselt es temporär im RAM und prüft die Koordinaten mathematisch via `shapely` gegen das vordefinierte Geofence-Polygon (`polygon.contains(point)`).
4. **Zustands-Steuerung (Invite / Kick):** 
   * Befindet sich der Nutzer **innerhalb** der Zone, wird über die Admin-API ein `POST /rooms/{info_room_id}/invite` abgesetzt.
   * Verlässt der Nutzer die Zone, wird er mittels `POST /rooms/{info_room_id}/kick` aus datenschutzrechtlichen Gründen der Datenhygiene unmittelbar entfernt.

---

## 3. JSON-Payload Struktur (Beispiel)

Das vom Edge-Client gesendete und vom Bot dechiffrierte Event entspricht dem standardisierten Matrix-Schema für Geodaten:

```json
{
  "content": {
    "geo_uri": "geo:51.9059,10.4289;u=15",
    "text": "Standort in Goslar, Deutschland"
  },
  "type": "m.location"
}
```
* `geo_uri`: Enthält Breitengrad, Längengrad und optional die Genauigkeit in Metern (`u=15`).
* `type`: Definiert das Event als standardisiertes Standort-Datenpaket.

---

## 4. Datenschutz-Fazit (DSGVO-Konformität)

* **Datenminimierung:** Es werden keine Bewegungshistorien oder Track-Profile dauerhaft gespeichert.
* **Privacy by Design:** Durch die Kopplung von dezentraler Föderation und Ende-zu-Ende-Verschlüsselung fließen sensible Koordinaten unlesbar über die Netzinfrastrukturen.
* **Transparenz:** Da der Gateway-Bot als unkommerzielles Open-Source-Infrastrukturprojekt vorliegt, ist der Code für Kommunen, Behörden und Bürger vollumfänglich auditierbar.
