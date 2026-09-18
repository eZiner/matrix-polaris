# 📱 POLARIS - Funktionale Spezifikation hyperlokaler Alltags-Module

* **Projekt-ID:** matrix-polaris
* **Komponente:** Strategischer Rahmen / Modulare Anwendungen
* **Version:** 1.0.0
* **Lizenz:** MIT-Lizenz (Open Source)
* **Status:** STRATEGIC SPECIFICATION (Ergänzungsplattform für leichtgewichtige Alltags-Anwendungen)

---

## 1. Das modulare Prinzip: Ergänzung statt System-Ersatz

POLARIS verzichtet bewusst darauf, komplexe und etablierte Enterprise-Ressource-Planning-Systeme (ERP) oder tiefgreifende Fachverfahren der kommunalen Verwaltung zu ersetzen. Das System versteht sich stattdessen als agile, offene Ergänzungsplattform.

Über standardisierte Matrix-Schnittstellen (wie Webhooks, Bots oder schlanke Dashboard-Widgets innerhalb von openDesk) können leichtgewichtige Programme und Apps eingeklinkt werden. Diese nutzen die Echtzeit-Fähigkeiten der PostGIS-Geofencing-Engine des Rathauses, um den Bürgern kontextbezogene, ortsabhängige Dienste direkt auf ihrem gewohnten Messenger-Endgerät anzubieten.

---

## 2. Kern-Spezifikation der ersten Alltags-Module

Drei exemplarische Module verdeutlichen die Synergie aus dezentralem Messenger-System und geografischer Kaskadierung:

### 2.1 Der Hyperlokale Mängelmelder

Dieses Modul ermöglicht es Bürgern, Schäden im öffentlichen Raum (z. B. ein tiefes Schlagloch, eine illegale Müllkippe oder eine defekte Straßenlaterne) direkt per Chat-Nachricht inklusive Foto an das Rathaus zu senden.

* **Der Geofencing-Workflow:** Der Bürger sendet die Meldung inklusive seiner aktuellen GPS-Koordinate in den lokalen Interaktionsraum.
* **Die PostGIS-Sortierung:** Das POLARIS-Gateway wertet die Koordinate flüchtig im RAM aus. Anstatt die Meldung in einen großen, unübersichtlichen stadtweiten Topf zu werfen, ermittelt das System das zuständige Ortsteil-Subpolygon (z. B. *Altstadt* oder *Ortsteil X*).
* **Die Zustellung:** Die Meldung wird vollautomatisch als strukturiertes Ticket direkt in den geschlossenen Matrix-Arbeitsraum des zuständigen Bauhofs oder der lokalen Straßenmeisterei gepusht – ohne manuelle Vorsortierung in der Zentrale.

### 2.2 Der Sub-Zonen-Abfallkalender

Klassische Abfallkalender-Apps scheitern im Alltag oft an komplexen Straßenlisten oder unübersichtlichen PDF-Tabellen. Das POLARIS-Abfall-Modul nutzt die Geometrie-Datenbank für eine vollautomatische Erinnerung.

* **Der Geofencing-Workflow:** Der Bürger hinterlegt in seinem privaten Vorgangsraum einmalig die Erlaubnis, seine Wohnadresse bzw. die dazugehörige Zone abzugleichen.
* **Die PostGIS-Sortierung:** Das System verknüpft die geografische Lage des Hauses mit den digitalen Abfuhrbezirken der Entsorgungsbetriebe, die als Layer in der PostGIS-Datenbank hinterlegt sind.
* **Die Zustellung:** Am Vorabend der Abholung sendet ein automatisierter Dienst eine schlichte, direkte Textnachricht in den Vorgangsraum des Bürgers (z. B. *„Moin! Morgen früh wird in deiner Straße die blaue Tonne geleert. Bitte rechtzeitig rausstellen.“*). Es ist keine separate App-Installation erforderlich.

### 2.3 Das digitale Schwarze Brett (Regionale Spaces)

Dieses Modul dient der Stärkung des lokalen Zusammenhalts, des Vereinslebens und der organisierten Nachbarschaftshilfe vor Ort.

* **Der Geofencing-Workflow:** Sobald der Bürger ein bestimmtes Ortsteil-Polygon betritt, schlägt die Hysterese-Engine an und klinkt ihn in das interaktive Forum der jeweiligen Sub-Zone ein.
* **Die Funktion:** Bürger können hier Angebote und Gesuche posten (z. B. *„Einkaufshilfe für Senioren gesucht“*, *„Flohmarkt im Vereinsheim am Samstag“*).
* **Datenhygiene:** Reist der Bürger ab (z. B. ein Pendler oder Tourist), sorgt der 10-minütige Cooldown dafür, dass das temporäre Schwarze Brett geräuschlos wieder von seiner Chat-Oberfläche verschwindet. Die App bleibt übersichtlich und frei von „Gruppenleichen“.

---

## 3. Technische Integration über openDesk

Die Verwaltung der Module erfolgt für die Mitarbeiter im Rathaus direkt über die gewohnte Weboberfläche des **openDesk Sovereign Workplace**.

* Über integrierte Matrix-Widgets (z. B. von Nordeck) können Sachbearbeiter eingehende Mängelmeldungen direkt in ihrer Nextcloud- oder Element-Umgebung sichten, priorisieren und den Status (z. B. *„In Bearbeitung“*) zurück in den verschlüsselten Vorgangsraum des meldenden Bürgers spiegeln.
* Dies sichert einen transparenten, schnellen und vollständig souveränen Informationskreislauf auf kommunaler Ebene.