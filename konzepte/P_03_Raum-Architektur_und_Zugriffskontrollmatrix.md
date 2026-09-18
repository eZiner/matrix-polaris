# 📊 POLARIS - Raum-Architektur und Zugriffskontrollmatrix

* **Projekt-ID:** matrix-polaris
* **Komponente:** Strategischer Rahmen / Berechtigungsstruktur
* **Version:** 1.0.0
* **Lizenz:** MIT-Lizenz (Open Source)
* **Status:** STRATEGIC SPECIFICATION (Funktionales Bereitstellungsmodell für Matrix-Administratoren)

---

## 1. Die vier Säulen der POLARIS-Raumtopologie

Um den behördlichen Informationsfluss rechtssicher zu gestalten und gleichzeitig das zivilgesellschaftliche Leben abzubilden, teilt POLARIS den kommunalen Kommunikationsraum in vier strikt isolierte Raumtypen auf. Jede dieser Kategorien besitzt eine eigene kaskadierende Berechtigungsstruktur.

### Raumtyp A: Der private Vorgangsraum (Individualkommunikation)

* **Charakter:** Streng vertraulich, unsichtbar für Dritte, Ende-zu-Ende verschlüsselt (Megolm-Ratchet).
* **Zweck:** Direkter Kanal zwischen Bürger und Fachamt für Bescheide, Statusmeldungen von Alltags-Modulen oder persönliche Antrags-Rückfragen.
* **Einklinken:** Permanent aktiv nach der Krypto-Token-Aktivierung im Bürgerbüro.

### Raumtyp B: K-12 Spaces (Bildungsinfrastruktur)

* **Charakter:** Geschlossene, rollenbasierte Räume für Schulen, Kitas und Bildungseinrichtungen.
* **Zweck:** Sicherer digitaler Austausch zwischen Schulleitung, Lehrkräften, Eltern und Schülern (z. B. für digitale Klassenbücher, Elternbriefe oder Vertretungspläne).
* **Einklinken:** Über verifizierte Einladungen oder Kopplung an städtische Schulregister.

### Raumtyp C: Die Geofencing-Info-Räume (Katastrophenschutz & Warnung)

* **Charakter:** Unverschlüsselt, öffentlich, geografisch kaskadiert, rein unidirektional.
* **Zweck:** Blitzschnelle Verteilung von behördlichen Warnungen (Katastrophenfall, Evakuierungen) oder lokalen Lageberichten (Großbaustellen, Straßensperren).
* **Einklinken:** Vollautomatisch über das *POLARIS Geofencing-Gateway* basierend auf den flüchtigen PostGIS-Koordinaten.

### Raumtyp D: Die regionalen Spaces (Öffentliches Vereins- & Bürgerleben)

* **Charakter:** Unverschlüsselt, öffentlich, geografisch kaskadiert, voll interaktiv.
* **Zweck:** Digitales schwarzes Brett, Austauschforen für Stadtteile, lokale Vereinsarbeit und Nachbarschaftshilfe.
* **Einklinken:** Synchron zu Raumtyp C über das Geofencing-Gateway, jedoch mit offenen Interaktionsrechten.

---

## 2. Die Zugriffskontrollmatrix (Power Levels)

Die Einhaltung der Informationsintegrität im föderierten Netzwerk wird über native Matrix-Berechtigungsstufen (*Power Levels*) gesteuert. Bürger (Standard-User) agieren mit Level 0, während die Verwaltung und automatisierte Gateways privilegierte Rechte besitzen.

| Raumtyp             | Benutzerrolle        | Power Level | Leserechte |  Schreibrechte  | Administrative Rechte (Kick/Ban) |
|---------------------|----------------------|:-----------:|:----------:|:----------------:|:--------------------------------:|
| **A: Vorgangsraum** | Bürger               |      0 |     Ja |        Ja |               Nein |
|                     | Fachamt              |     100 |     Ja |        Ja |                Ja |
| **B: K-12 Spaces**  | Schüler / Eltern     |      0 |     Ja |  Eingeschränkt  |               Nein |
|                     | Lehrkraft / Leitung  |  50 / 100  |     Ja |        Ja |                Ja |
| **C: Info-Räume**   | Bürger               |      0 |     Ja | **Nein (Muted)** |               Nein |
|                     | Krisenstab / Gateway |     100 |     Ja |        Ja |                Ja |
| **D: Reg. Spaces**  | Bürger               |      0 |     Ja |        Ja |               Nein |
|                     | Lokaler Moderator    |     50  |     Ja |        Ja |                Ja |
|                     | Kommunales Gateway   |     100 |     Ja |        Ja |   Ja (Zentraler Troll-Schutz)  |

---

## 3. Technischer Schutz vor Spam und Fremdinfiltration (Troll-Schutz)

Da das Matrix-Protokoll ein weltweites, offenes Föderationsnetzwerk beschreibt, implementiert POLARIS zwei serverseitige Schutzmechanismen auf dem lokalen **SLG-Rack** im Rathaus:

1. **Restricted Join Rules:** Die Räume des Typs B (K-12) und D (Regionale Spaces) sind über native Matrix-Sicherheitsregeln zwingend an den übergeordneten, verifizierten Mutter-Space der jeweiligen Kommune gekoppelt. Ein Beitritt ist nur für Konten zulässig, die eine amtlich verifizierte Identität der Gemeinde besitzen (z. B. `@user:goslar.de`).
2. **Föderations-Barriere:** Externe, nicht-validierte Server (wie z. B. `matrix.org` oder fremde kommerzielle Instanzen) werden an der Föderationsgrenze des Rathaus-Homeservers für sensible Raumtypen automatisch abgewiesen. Dies verhindert zuverlässig das Eindringen von Spam-Bots, digitalem Vandalismus oder Desinformationskampagnen in den kommunalen Kommunikationsraum.