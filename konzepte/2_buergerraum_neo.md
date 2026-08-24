# 📑 Projekt POLARIS: Der Bürgerraum

**Konzept und barrierefreie Benutzeroberflächen auf Basis der FITKO-Initiative „Neo“**

**Autor:** eZiner  
**Status:** Konzeptpapier (Zielgruppen: IT-Architekten, Datenschutzbeauftragte, Amtsleitung)  
**Datum:** August 2026  

---

## 1. Das „Postersatz-Modell“

POLARIS setzt auf das physische Bürgerbüro als Vertrauensanker statt auf einen Zwang zu Online-Ausweisen oder zentralen Bundesregistern.

### Der administrative Registrierungs-Ablauf:
1. **Analoge Identifikation:** Einmalige Legitimation direkt vor Ort im Bürgerbüro.
2. **Kryptografische Token-Generierung:** Erstellung eines Matrix-Kontos im städtischen Dashboard und Ausgabe eines QR-Codes.
3. **Erstanmeldung:** Scannen des QR-Code-Aktivierungs-Tokens zu Hause für den sofortigen Zugang.

---

## 2. Die BundID-Alternative: Souveräne Kommunal-Homeserver (FITKO Neo-Interoperabilität)

Ein zentraler Grundpfeiler von Projekt POLARIS ist der **vollständige Verzicht auf die staatliche BundID-Infrastruktur** (NFC-Zwang, Elster-Zertifikate, zentrale Kontenregister). Um dieses Ziel zu erreichen und dennoch das moderne, offizielle Benutzerinterface des [FITKO-Projekts „Neo“](https://gitlab.opencode.de/fitko/matrix-g2x) zu nutzen, ist **kein Code-Fork** nötig. POLARIS nutzt stattdessen die offene Schnittstellen-Architektur des Matrix-Standards.

```text
[ FITKO Neo-Apps / Web-Frontend ] ➔ (Standard Matrix API)
                                           |
                                           v
                 [ Kommunaler Homeserver (z.B. matrix.duesseldorf.de) ]
                                           ^
                                           | (Lokale Nutzerauthentifizierung)
                            [ POLARIS Postersatz-Modell ]
```

### Das Schnittstellen-Prinzip statt Fork-Aufwand
Das Bundesprojekt Neo besteht im Kern aus Open-Source-Frontends (Web-App, iOS- und Android-Clients), die über die standardisierte Matrix-Client-Server-API mit einem Server kommunizieren. Die BundID ist darin lediglich als ein austauschbares Login-Modul (Identity Provider) angehängt. 

POLARIS kappt diese Schnittstelle zur BundID auf Serverebene und ersetzt sie durch das **Postersatz-Modell**:
*   **Kommunale Datenhoheit:** Jede Kommune betreibt einen eigenen, souveränen Matrix-Homeserver (z. B. `matrix.duesseldorf.de`).
*   **Physischer Vertrauensanker:** Die Verifizierung und Erstellung der Bürger-Accounts erfolgt direkt und datenschutzkonform bei der analogen Identitätsprüfung im lokalen Bürgerbüro. Es wird ein kryptografischer Token erzeugt.
*   **Nahtlose UI-Kompatibilität:** Der Bürger kann die offiziellen, barrierefreien Neo-Frontends der FITKO uneingeschränkt nutzen. Beim Login verbindet sich die App jedoch nicht mit einer Bundes-Zentraldatenbank, sondern authentifiziert sich direkt am lokalen, kommunalen Homeserver über den im Bürgerbüro erhaltenen Token.

### Rechtliche Rahmenbedingungen und freie Nachnutzung (Public Code)
Da das Projekt Neo der FITKO unter einer freien Open-Source-Lizenz im offiziellen OpenCoDE-Repository des Bundes bereitgestellt wird, ist für die Integration in POLARIS **kein Code-Fork** erforderlich. Die Nachnutzung und Anpassung der Benutzeroberfläche erfolgt im Rahmen der Open-Source-Vorgaben der öffentlichen Hand („Public Money? Public Code!“). Es werden lediglich die standardisierten Client-Schnittstellen (APIs) bedient, um die Neo-Userinterfaces direkt an die dezentralen, kommunalen POLARIS-Homeserver anzubinden.

---

## 3. Raum-Architektur und Daten-Isolation

POLARIS trennt strikt zwischen verschlüsselten, privaten Behörden-Vorgängen und öffentlichen, ortsabhängigen Informationsströmen.

### A. Der Vorgangsraum (Amtliche Individualkommunikation)
* **Zweck:** Direkter Austausch zwischen Bürger und Fachamt (z. B. Parkgenehmigungen).
* **Verschlüsselung:** Zwingend Ende-zu-Ende verschlüsselt mittels des modernen Megolm-Protokolls.
* **Berechtigungen (Power Levels):** Bürger besitzt Stufe 0 (Lesen/Schreiben), das städtische Fachamt besitzt Stufe 100 (Administrator).
* **Sichtbarkeit:** Privat. Räume sind im globalen Föderationsnetzwerk für unbefugte Dritte und Crawler komplett unsichtbar.

### B. K12 Spaces (Bildungsstätten)
* **Zweck:** Austausch zwischen Schülern, Eltern und Lehrern.
* **Verschlüsselung:** Unterschiedlich.
* **Berechtigungen (Power Levels):** Gesonderte Rechtevergabe.
* **Sichtbarkeit:** Privat. Räume sind im globalen Föderationsnetzwerk für unbefugte Dritte und Crawler komplett unsichtbar.

### C. Info-Räume (Geofencing, Tourismus & Katastrophenschutz)
* **Zweck:** Regionale Warnungen und Tourismus-Informationen in Echtzeit.
* **Verschlüsselung:** Unverschlüsselt, da es sich um rein öffentliche Daten handelt.
* **Berechtigungen (Power Levels):** 
  * Bürger: Stufe 0 (Reines Leserecht, keine Schreibrechte zur Verhinderung von Spam).
  * POLARIS-Gateway: Stufe 50 (Moderator-Rechte für automatische Einladungen und Raum-Kicks).
  * Kommunale Redaktion: Stufe 100 (Administrator-Rechte für das Verfassen von Meldungen).
* **Sichtbarkeit:** Öffentlich innerhalb der Föderation, jedoch ist der Beitritt primär an die Einladung (Invite) durch das Geo-Gateway gekoppelt.

### D. Regionale Spaces (Geofencing, Räume für Vereine, Foren und Marktplätze)
* **Zweck:** Kommunale Kommunikationsplattform.
* **Verschlüsselung:** Unverschlüsselt, da es sich um rein öffentliche Daten handelt.
* **Berechtigungen (Power Levels):** 
  * Bürger: Stufe 0 (Lesen/Schreiben).
  * POLARIS-Gateway: Stufe 50 (Moderator-Rechte für automatische Einladungen und Space-Kicks).
  * Kommunale Moderatoren: Stufe 50.
  * Kommunale Redaktion: Stufe 100 (Administrator-Rechte für das bestimmen von Moderatoren).
* **Sichtbarkeit:** Öffentlich innerhalb der Föderation, jedoch ist der Beitritt primär an die Einladung (Invite) durch das Geo-Gateway gekoppelt.

---

## 4. Datenschutzkonformes Bereinigungskonzept

Ein Kernvorteil in POLARIS ist die automatische Datenhygiene auf dem Endgerät. Im Gegensatz zu kommerziellen Messengern verbleiben ortsbasierte Info-Kanäle nach der Abreise des Bürgers nicht als ungenutzte Gruppenleichen in der Chat-Übersicht. Das serverseitige Gateway initiiert nach dem Verlassen einer Zone und dem Ablauf der Karenzzeit ein koordiniertes Austritts-Protokoll, wodurch der Raum restlos und sauber aus der App des Nutzers entfernt wird.
