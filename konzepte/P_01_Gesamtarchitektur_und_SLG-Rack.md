# 🏛️ POLARIS - Gesamtarchitektur und das SLG-Rack-Modell

* **Projekt-ID:** matrix-polaris
* **Komponente:** Strategischer Rahmen / Architekturkonzept
* **Version:** 1.0.0
* **Lizenz:** MIT-Lizenz (Open Source)
* **Status:** STRATEGIC SPECIFICATION (Konzeptioneller Leitfaden für Kommunen und IT-Dezernenten)

---

## 1. Das Sovereign Local Government Rack (SLG-Rack)

Das physische und logische Rückgrat des POLARIS-Netzwerks ist das **Sovereign Local Government Rack (SLG-Rack)**. Um absolute Autonomie, Ausfallsicherheit und digitale Souveränität zu garantieren, wird diese standardisierte Server-Infrastruktur **zwingend physisch vor Ort im lokalen Rathaus** betrieben.

Es bricht die Abhängigkeit von zentralisierten Bundessilos oder globalen Cloud-Monopolen auf und verankert die Daten und Dienste dort, wo sie hingehören: in der rechtlichen und physischen Hoheit der Kommune.

### Die drei Kern-Komponenten im Rack:

1. **Der Kommunale Matrix-Homeserver:** Verwaltet die lokalen Benutzerkonten und verschlüsselten Räume. Er schließt sich dezentral mit den Servern anderer Gemeinden zu einem ausfallsicheren, bundesweiten Föderations-Mesh-Netzwerk zusammenschließt.
2. **Der openDesk Sovereign Workplace:** Der Open-Source-Arbeitsplatz des Bundes (Nextcloud, Open-Xchange, Element) läuft lokal auf dem Rack. Durch das Einsparen proprietärer Lizenzgebühren refinanziert sich die Hardware-Infrastruktur des Racks organisch selbst.
3. **Das POLARIS Geofencing-Gateway:** Der algorithmische Infrastrukturdienst (PostGIS/Python), der Standortereignisse flüchtig auswertet und den automatisierten Raum-Beitritt steuert.

---

## 2. Das dreischichtige Sicherheits- und Schichtenmodell

Die Netzwerktopologie von POLARIS trennt die Datenströme strikt in drei logische Bereiche, um die interne Verwaltungs-IT maximal zu schützen und gleichzeitig den Bürgerservice im öffentlichen Raum zu realisieren:

### Schicht 1: Die interne Behördenschicht

Das hochgesicherte, interne Kernnetzwerk der Verwaltung bleibt eine unangreifbare Festung. Sachbearbeiter nutzen openDesk und interne Fachanwendungen lokal. Es gibt keine direkten Inbound-Verbindungen aus dem Internet in diese Schicht. Informationen, Warnmeldungen oder Daten für Alltags-Module werden ausschließlich kontrolliert von innen nach außen (Outbound) in die demilitarisierte Zone (DMZ) gepusht.

### Schicht 2: Die kommunale DMZ (Demilitarisierte Zone / Vorfeld)

Der öffentlich erreichbare Vorposten des Rathauses, auf dem das physische SLG-Rack operiert. Hier laufen der Matrix-Homeserver und das Geofencing-Gateway. Diese Schicht nimmt die verschlüsselten Client-Verbindungen der Bürger sowie die Föderations-Datenströme anderer Kommunen entgegen. Sie dient als digitaler Postkasten und Schutzpuffer vor dem internen Netz.

### Schicht 3: Die Bürger-Ebene

Der Endpunkt des Netzwerks. Bürger greifen über das Internet (oder im Krisenfall über lokale Netze) auf die kommunale DMZ zu. Sie nutzen hierfür genau ein einziges verifiziertes Konto (z. B. `@max:goslar.de`) in einem Standard-Messenger ihrer Wahl (z. B. Element, FluffyChat). Es ist keinerlei Modifikation der Apps oder ein proprietärer App-Zwang erforderlich.

---

## 3. Der autarke Krisen-Inselmodus

Ein zentraler strategischer Vorteil der physischen Regionalkomponente im Rathaus ist die Resilienz bei Katastrophenlagen (z. B. großflächiger Stromausfall, Blackout oder die physische Kappung überregionaler Glasfaser-Überlandleitungen):

* **Lokale Autarkie:** Fällt die Verbindung zum globalen Internet aus, schaltet das SLG-Rack im Rathaus augenblicklich in den autarken Inselmodus.
* **Mesh- und Notfunk-Kopplung:** Über die Anbindung an lokale, krisensichere Richtfunk-, Mesh- oder behördliche Notfunk-Netzwerke bleibt der Matrix-Homeserver innerhalb des Gemeindegebiets für die lokale Bevölkerung erreichbar.
* **Funktionserhalt:** Bürger können weiterhin in Echtzeit Notrufe absetzen, sich in den hyperlokalen Ortsteilkanälen koordinieren und auf lokal auf dem Rack gespiegelte openDesk-Dienste oder Notfall-Alltagsmodule zugreifen. Die Kommunikation bricht genau dann nicht zusammen, wenn das öffentliche Netz versagt.