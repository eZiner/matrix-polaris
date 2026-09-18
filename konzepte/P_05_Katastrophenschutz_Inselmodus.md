# 📡 POLARIS - Katastrophenschutz und der autarke Inselmodus

* **Projekt-ID:** matrix-polaris
* **Komponente:** Strategischer Rahmen / Krisen-Resilienz
* **Version:** 1.0.0
* **Lizenz:** MIT-Lizenz (Open Source)
* **Status:** STRATEGIC CONCEPT (Ergänzungsmodul für den kommunalen Katastrophenschutz)

---

## 1. Das Szenario: Der überregionale Infrastruktur-Blackout

Bei gravierenden Schadenslagen (wie extremen Unwetterereignissen, Hochwasser oder gezielten Cyberangriffen auf die Backbone-Infrastruktur) bricht die überregionale Internet- und Mobilfunkanbindung häufig als Erstes zusammen.

Kommerzielle Cloud-Dienste und zentralisierte Kommunikationssysteme sind in diesem Moment für die lokale Bevölkerung und die Einsatzkräfte vor Ort nutzlos. POLARIS fängt diese kritische Schwachstelle ab, indem es die physische Präsenz des **Sovereign Local Government Racks (SLG-Rack) direkt im Rathaus** ausnutzt.

---

## 2. Der logische und physische Inselmodus (Local Autonomy)

Sobald die überregionale Verbindung abreißt, schaltet das SLG-Rack automatisch in den **autarken Inselmodus**. Da der kommunale Matrix-Homeserver (z. B. Synapse) und die PostGIS-Geofencing-Engine lokal auf der Hardware im Rathaus laufen, bleibt die gesamte Kerninfrastruktur funktionsfähig:

* **Lokale Nutzer-Authentifizierung:** Die Bürger-Accounts und die im Bürgerbüro generierten Krypto-Token werden lokal auf dem Rack validiert. Ein passwortloser Login und die Verschlüsselung funktionieren ohne Verbindung zu externen Servern.
* **Flüchtige Datenverarbeitung vor Ort:** Das POLARIS-Gateway wertet eintreffende Standortsignale weiterhin flüchtig im RAM aus. Die kaskadierte PostGIS-Prüfung läuft lokal und unabhängig von globalen Netzen.
* **openDesk-Verfügbarkeit:** Lokal gespiegelte Dokumente, Notfallpläne und Einsatzrichtlinien auf der Nextcloud-Instanz des Racks bleiben für die Verwaltung sofort abrufbar.

---

## 3. Die Netzkopplung über krisensichere Lokal-Netzwerke

Um den Bürgerraum und die Info-Kanäle im Inselmodus für die Bevölkerung erreichbar zu machen, dockt das SLG-Rack an unabhänige, lokale Transport-Schichten an:

* **Kommunale Mesh-Netzwerke:** Die Kopplung mit dezentralen Freifunk- oder ad-hoc WLAN-Mesh-Strukturen im Innenstadtbereich ermöglicht es Bürgern, sich ohne Mobilfunknetz direkt mit dem Rathaus-Homeserver zu verbinden.
* **Richtfunk-Strecken:** Lokale Richtfunk-Verbindungen zwischen dem Rathaus und kritischen Außenstellen (z. B. Feuerwehrhäusern, Notunterkünften oder Energieversorgern) halten das regionale Matrix-Föderationsnetzwerk innerhalb der Gemeindegrenzen aufrecht.
* **Behördliche Notfunk-Infrastruktur:** Das Einbinden von schmalbandigen Datenkanälen (z. B. über spezifische Funkfrequenzen oder Satelliten-Backup-Links) stellt sicher, dass das Gateway Lageberichte und offizielle Warnungen empfangen und verteilen kann.

---

## 4. Hyperlokaler Krisen-Workflow im Betrieb

Im Akutfall wird das Zusammenspiel aus Geofencing-Gateway und Matrix-Infrastruktur zum zentralen Werkzeug der zivilen Krisenbewältigung:

1. **Erzwungener Krisenraum-Beitritt (Strict Override):** Das Gateway setzt den geschwindigkeits-adaptiven Transit-Filter außer Kraft. Jede Koordinate, die das lokale Notnetz erreicht, führt zum sofortigen Beitritt des Bürgers in den unverschlüsselten, stummen Katastrophen-Warnkanal der Gemeinde.
2. **Gesteuerte Bürger-Information:** Die kommunale Redaktion (oder die Katastrophenschutzleitung) sendet offizielle Lageberichte, Verhaltensanweisungen und Standorte von Notausgabestellen (z. B. Trinkwasser, Notstrom) in den Warnkanal. Durch das restriktive *Power Level 0 (Muted)* für Bürger bleibt der Kanal frei von Spam und Panikmache.
3. **Dezentraler Hilfe-Kanal (Der Bürgerraum):** Parallel dazu dient der lokale Bürgerraum als Marktplatz für organisierte Nachbarschaftshilfe (z. B. „Suche medizinisches Personal in Ortsteil X“ oder „Biete privaten Generator in Sub-Zone Y“).