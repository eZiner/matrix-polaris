# 🌌 POLARIS - Das digital souveräne Bürgernetzwerk

POLARIS ist der strategische und architektonische Überbau für ein dezentrales, krisensicheres Kommunikations- und Dienstenetzwerk auf kommunaler Ebene.

Das Projekt bricht proprietäre App-Silos in der öffentlichen Verwaltung auf. Gestützt auf das standardisierte **Matrix-Protokoll** und den Open-Source-Arbeitsplatz **openDesk** des Bundes entsteht eine schlanke Infrastruktur, die den digitalen Bürgerservice datenschutzkonform, barrierefrei und unter vollständiger eigener Kontrolle der lokalen Verwaltung realisiert.

🌐 **Projektseite & Dokumentation:** [eziner.github.io/matrix-polaris](https://eziner.github.io/matrix-polaris/)  
📄 **Lizenz:** MIT-Lizenz (Open Source)  
🚦 **Status:** STRATEGIC OVERVIEW & FRAMEWORK (Überbau im Aufbau / Daten-Prototyp funktionsfähig)

---

## 🏛️ Das Fundament: Das SLG-Rack im Rathaus

Das physische und logische Rückgrat von POLARIS ist das **Sovereign Local Government Rack (SLG-Rack)**. Um echte Autonomie und Ausfallsicherheit zu gewährleisten, wird diese standardisierte Server-Infrastruktur **zwingend physisch vor Ort im lokalen Rathaus** betrieben.

Das SLG-Rack bündelt die Kernkomponenten für den souveränen Kommunalbetrieb:

1. **Der Kommunale Matrix-Homeserver (Infrastruktur-Layer):** Betreibt die dezentrale Instanz (z. B. via Synapse), die sich mit anderen Kommunen in einem ausfallsicheren, bundesweiten Föderations-Mesh-Netzwerk zusammenschließt.
2. **Der openDesk Sovereign Workplace (Collaboration-Layer):** Integriert den Open-Source-Arbeitsplatz des Bundes für die Verwaltung (Nextcloud, Open-Xchange, Element). Die Infrastruktur refinanziert sich organisch durch das Einsparen proprietärer Software-Lizenzen.
3. **Das POLARIS Geofencing-Gateway (Algorithmic Layer):** Ein intelligenter Infrastrukturdienst, der über eine PostGIS-Geodatenbank standardisierte, flüchtige Standortsignale in Echtzeit auswertet und Bürger vollautomatisch in regionale Informationsräume ein- und auswählt.

---

## 🚀 Die Vision: Hyperlokale Alltags-Module statt System-Ersatz

POLARIS versteht sich ausdrücklich **nicht als Ersatz für bestehende, komplexe Dienstenetzwerke oder etablierte Fachverfahren** der Verwaltung. Das System ist vielmehr eine agile Ergänzungsplattform, um den lokalen Alltag der Bürger durch standortbasierte Anwendungen zu erleichtern.

Durch das Zusammenspiel mit dem **Geofencing-Gateway** lassen sich leichtgewichtige, kontextbezogene Module und Apps direkt in das Messenger-Ökosystem integrieren:

* **Geobasierte Alltags-Dienste:** Einfache Programme wie automatisierte, ortsteilgenaue Abfallkalender, hyperlokale Wettermeldungen oder digitale schwarze Bretter für die Nachbarschaftshilfe passen sich dynamisch dem aktuellen Standort des Bürgers an.
* **Kommunaler Mängelmelder:** Bürger können Schäden (z. B. Schlaglöcher oder defekte Straßenbeleuchtung) direkt über das gewohnte Messenger-Interface melden. Das Gateway sortiert die Meldung anhand der PostGIS-Koordinaten vollautomatisch in den zuständigen kommunalen Arbeitsraum ein.
* **Autarker Krisen-Inselmodus:** Fällt die überregionale Internetanbindung komplett aus, schaltet das physisch im Rathaus installierte SLG-Rack in den **autarken Inselmodus**. Die Kommunikation innerhalb der Gemeinde sowie der Zugriff auf die lokalen Alltags-Module bleiben über regionale Notfunk- oder Mesh-Netzwerke für die Bevölkerung uneingeschränkt funktionsfähig.

---

## 📂 Verzeichnisstruktur dieses Repositories

Dieses übergeordnete Repository koordiniert die strategische Konzeption und die politischen Whitepapers für Entscheidungsträger:

* `/konzepte`: Whitepaper zur Gesamtarchitektur, dem *Postersatz-Modell*, der Geofencing-Kaskade und dem föderierten Datennetz.
* `/analysen` *(Im Aufbau)*: Betriebswirtschaftliche Szenarienkalkulationen und 10-Jahres-Kostenprognosen am Beispiel von Kommunen (z. B. der Bergstadt Clausthal-Zellerfeld).
* `/antraege` *(Im Aufbau)*: Standardisierte Vorlagen für Ratsbeschlüsse, Haushaltsanträge und politische Entscheidungsvorlagen.

Der einsatzbereite Quellcode der Geofencing-Engine und der Datenbank-Pipeline befindet sich im dedizierten Entwicklungs-Repository:  
👉 [**matrix-polaris-gateway (GitHub-Repository)**](https://github.com/eZiner/matrix-polaris-gateway)

---

## 🔒 Datenschutz & Sicherheit (Privacy by Design)

* **Keine Bewegungsprofile:** Geokoordinaten werden flüchtig im RAM-Cache der PostGIS-Datenbank ausgewertet und nach der Raumzuordnung sofort unwiderruflich verworfen.
* **Hysterese-Schutz:** Ein algorithmischer 10-Minuten-Cooldown verhindert kaskadierende Serverlasten und Signal-Springen an Regionsgrenzen.
* **Identitätssouveränität:** Verzicht auf den Zwang von eID- oder BundID-Registrierungen im Alltag durch die Nutzung des lokalen Bürgerbüros als analogen Vertrauensanker.

---

## 👥 Mitmachen & Community

POLARIS versteht sich als gesamtgesellschaftliches Infrastrukturprojekt für die digitale Souveränität im kommunalen Raum. Wir laden Entwickler, Netzwerk-Architekten, Kommunalpolitiker und Verwaltungs-Spezialisten herzlich ein, an den Konzepten und der Implementierung mitzuwirken.

---

## ⚠️ Status des Projekts & Haftungsausschluss

**Dieses Projekt ist ein privater, nicht-kommerzieller Prototyp (Proof of Concept).**

Bitte beachten Sie für die Nutzung und Weiterentwicklung folgende Punkte:

- **Entwicklungsstatus:** Diese Software befindet sich im Prototypen-Stadium. Sie ist NICHT für den produktiven Einsatz bereit und sollte ohne vorherige, unabhängige Sicherheitsprüfungen nicht in Live-Systemen eingesetzt werden.
- **KI-Unterstützung:** Teile dieses Codes und der Dokumentation wurden mit Unterstützung von Google-KI-Werkzeugen erstellt. Alle Inhalte wurden vom Autor manuell geprüft, überarbeitet und getestet. Eine absolute Fehlerfreiheit kann jedoch nicht garantiert werden.
- **Haftungsausschluss:** Entsprechend der MIT-Lizenz wird die Software ohne jegliche Gewährleistung ("as is") zur Verfügung gestellt. Der Autor übernimmt keine Haftung für Systemfehler, Datenverlust, Sicherheitslücken oder etwaige Lizenzkonflikte durch eingebundene Pakete (Dependencies).
- **Zukünftige Trägerschaft:** Dieses Repository dient als technische Grundlage. Es ist vorgesehen, dass die finale redaktionelle, technische und rechtliche Verantwortung sowie die Code-Audits vor einem produktiven Rollout an eine öffentliche Institution oder eine gemeinnützige Organisation (NGO) übertragen werden.