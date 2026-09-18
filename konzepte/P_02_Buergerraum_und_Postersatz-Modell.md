# 📑 POLARIS - Der Bürgerraum und das Postersatz-Modell

* **Projekt-ID:** matrix-polaris
* **Komponente:** Strategischer Rahmen / Bürger-Identität
* **Version:** 1.0.0
* **Lizenz:** MIT-Lizenz (Open Source)
* **Status:** STRATEGIC SPECIFICATION (Konzeptioneller Leitfaden für Kommunen und IT-Dezernenten)

---

## 1. Die Vision des Bürgerraums (Integration von FITKO Neo)

Der „Bürgerraum“ bildet die zentrale digitale Schnittstelle zwischen der Bevölkerung und der kommunalen Verwaltung. Um wertvolle Entwicklungsressourcen zu schonen und das Rad nicht neu zu erfinden, dockt POLARIS an bestehende Open-Source-Initiativen des Bundes an.

Als barrierefreie und benutzerfreundliche Weboberfläche kommt das offizielle Frontend **„Neo“** der Föderalen IT-Kooperation (FITKO) zum Einsatz. Dieses wird ohne eigenen Code-Fork direkt über die standardisierten Client-Server-APIs des lokalen **Sovereign Local Government Racks (SLG-Rack)** im Rathaus ausgeliefert. Der Bürger erhält dadurch eine schlanke, behördlich geprüfte Web-App, behält aber im Sinne des Matrix-Standards die Freiheit, jeden anderen kompatiblen Open-Source-Messenger (wie *Element* oder *FluffyChat*) zu nutzen.

---

## 2. Das Postersatz-Modell als analoger Vertrauensanker

Die größte Hürde digitaler Verwaltungsprozesse ist die sichere Erstidentifikation. Die Nutzung der Online-Ausweisfunktion (eID) oder zentralisierter BundID-Konten scheitert in der kommunalen Praxis oft an technischer Komplexität (NFC-Probleme auf Smartphones, gesperrte PINs) oder mangelnder Akzeptanz in der Bevölkerung.

POLARIS löst dieses Problem durch das **Postersatz-Modell**, welches das physische **Bürgerbüro im Rathaus** als unumstößlichen Vertrauensanker (*Root of Trust*) nutzt:

1. **Physische Legitimation:** Der Bürger identifiziert sich einmalig persönlich im Bürgerbüro (z. B. im Rahmen einer regulären Meldebestätigung oder Ausweisabholung) per Lichtbildausweis gegenüber dem Sachbearbeiter.
2. **Kryptografische Freischaltung:** Der Sachbearbeiter aktiviert den Account über das städtische openDesk-Dashboard auf dem lokalen Server. Das System generiert daraufhin ein flüchtiges, zeitlich begrenztes kryptografisches Aktivierungs-Token.
3. **Der analoge Beleg:** Dieses Token wird als QR-Code auf einem physischen Beleg ausgedruckt (analog zum klassischen PostIdent-Verfahren der Post, daher *Postersatz-Modell*) und dem Bürger direkt ausgehändigt.

---

## 3. Passwortloser Zugang und gerätespezifische Sicherheit

Mit dem QR-Code-Beleg in der Hand ist die Einrichtung für den Bürger zu Hause in Sekunden und ohne technische Vorkenntnisse abgeschlossen:

* **Scan & Go:** Der Bürger scannt den QR-Code mit der Kamera seines Smartphones, Tablets oder Laptops.
* **Serverseitige Zuordnung:** Die App liest das Token aus und meldet sich am lokalen Matrix-Homeserver (z. B. `matrix.goslar.de`) des SLG-Racks an.
* **Kryptografische Schlüsselgenerierung:** Das System erzeugt vollautomatisch die notwendigen gerätespezifischen Identitäts- und Verschlüsselungsschlüssel.

Der Zugang erfolgt fortan **vollständig passwortlos**. Da keine Passwörter auf den Servern des Rathauses hinterlegt sind, entfällt das Risiko von Identitätsdiebstählen durch gestohlene Passwort-Datenbanken. Der Account ist kryptografisch fest an das verifizierte Endgerät gekoppelt.