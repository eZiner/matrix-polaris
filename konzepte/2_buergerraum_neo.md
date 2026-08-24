# 📑 Projekt POLARIS: Der Bürgerraum

**Konzept und barrierefreie Benutzeroberflächen auf Basis der FITKO-Initiative „Neo“**

**Autor:** eZiner  
**Status:** Konzeptpapier (Zielgruppen: IT-Architekten, Datenschutzbeauftragte, Amtsleitung)  
**Datum:** August 2026  

---

## 1. Das „Postersatz-Modell“: Souveräne Alternative zum BundID-Zwang

POLARIS setzt auf das physische Bürgerbüro als Vertrauensanker statt auf einen Zwang zu Online-Ausweisen oder zentralen Bundesregistern.

### Der administrative Registrierungs-Ablauf:
1. **Analoge Identifikation:** Einmalige Legitimation direkt vor Ort im Bürgerbüro.
2. **Kryptografische Token-Generierung:** Erstellung eines Matrix-Kontos im städtischen Dashboard und Ausgabe eines QR-Code-Aktivierungs-Tokens.
3. **Erstanmeldung:** Scannen des QR-Codes zu Hause für den sofortigen, passwortfreien Zugang.

---

## 2. Das Schnittstellen-Prinzip: FITKO „Neo“ ohne BundID-Kopplung

Das System baut auf der FITKO-Initiative „Neo“ auf. Da die Frontends als Open-Source Public-Code bereitgestellt werden, ist kein Code-Fork notwendig. Details zur vollen UI-Nachnutzung des barrierefreien User-Interfaces des Bundes sowie die vollständigen Markdown-Inhalte können den referenzierten Dokumenten entnommen werden.

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
