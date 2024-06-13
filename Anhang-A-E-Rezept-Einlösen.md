# Anhang A - Einlösen von E-Rezepten

## A.1 Ablauf beim Einlösen von E-Rezepten

Der grundlegende Ablauf für das Einlösen von E-Rezepten ist im nachfolgenden Sequenzdiagramm dargestellt. 

![Ablauf zum Einlösen von E-Rezepten in einer Apotheke](https://github.com/eHealthCardLink/Spezifikation/blob/main/E-Rezept-Einl%C3%B6sen-Ablauf.svg)

* `(0)-(7)` - Bevor E-Rezepte in einer Apotheke mit dem eHealth-CardLink-Verfahren eingelöst werden können, müssen die notwendigen Schritte im [Basisablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/Spezifikation.md) durchgeführt werden.
* `(8)` - In diesem Schritt muss für die spätere Kommunikation mit dem E-Rezept-Fachdienst (FD_eRp) beim IDP-Dienst eine Authentifizierung des AVS erfolgen, um ein verschlüsseltes ID-Token und Access-Token zu erhalten. Weitere Details zu diesem Ablauf finden sich in [gemSpec_IDP_FD](https://fachportal.gematik.de/fachportal-import/files/gemSpec_IDP_FD_V1.7.1.pdf) (Abschnitt 5.5). 
* `(9)` - Bevor fachliche Anfragen an den FD_eRp gestellt werden können, muss eine Verbindung mit der "Vertrauenswürdigen Ausführungsumgebung" (VAU) des FD_eRp aufgebaut werden. Details hierzu finden sich in [gemSpec_FD_eRp](https://fachportal.gematik.de/fachportal-import/files/gemSpec_FD_eRp_V2.1.0.pdf) (Abschnitt 5.8.5) und [gemSpec_Krypt](https://fachportal.gematik.de/fachportal-import/files/gemSpec_Krypt_V2.32.0.pdf) (Abschnitte 3.16 und 7).
* `(10)` - Unter Verwendung des Prüfungsnachweises, der in Schritt 7 aus dem [Basisablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/Spezifikation.md) erhalten wurde, werden die verfügbaren E-Rezept-Tasks ermittelt.
* `(11)` - Die verfügbaren E-Rezept-Tasks werden in einem Bundle zurückgeliefert. 
* `(12)` - In diesem Schritt wird das erhaltene Bundle dekodiert.
* `(13)` - In einer Schleife über alle verfügbaren E-Rezepte wird unter Verwendung des Prüfungsnachweises das jeweilige E-Rezept angefordert.
* `(14)` - Das individuelle E-Rezept wird zusammen mit einem Geheimnis (secret) als PKCS#7-MIME-codierte MedicationReference vom FD_eRp zurückgeliefert.
* `(15)` - Jedes so erhaltene E-Rezept wird dekodiert und es wird die "E-Rezept-Zusammenfassung" (PrescriptionSummary) zur weiteren Verarbeitung extrahiert.
* `(16)` - Die verfügbaren "E-Rezept-Zusammenfassungen" werden in einer Liste (AvailablePrescriptionList) zusammengefasst und über die in Schritt 7.1 etablierte Websocket-Verbindung an den eHealth-CardLink-Dienst übertragen.
* `(17)` - Der eHealth-CardLink-Dienst reicht die Liste mit den "E-Rezept-Zusammenfassungen" (AvailablePrescriptionList) weiter an die App.
* `(18)` - Die App zeigt dem Nutzer die Liste der verfügbaren E-Rezepte an.
* `(19)` - Der Nutzer wählt die zu dispensierenden E-Rezepte aus und erfasst ggf. weitere Daten zur Belieferung des E-Rezepts.
* `(20)` - Die vom Nutzer ausgewählten E-Rezepte (SelectedPrescriptionList) werden von der App an den eHealth-CardLink-Dienst übertragen.
* `(21)` - Die Liste mit den ausgewählten E-Rezepten (SelectedPrescriptionList) wird vom eHealth-CardLink-Dienst ans AVS übertragen.
* `(22)` - Für alle zur Dispensierung ausgewählten E-Rezepte wird vom AVS an den FD_eRp eine $accept-Nachricht gesendet.
* `(23)` - Der FD_eRp liefert ein Bundle mit einer Task und einen PKCS#7-Container mit dem signierten E-Rezept zurück.
* `(24)` - Das erhaltene Bundle wird dekodiert und das enthaltene secret wird gespeichert.
* `(25)` - Die qualifizierte elektronische Signatur, die die E-Rezept-Nutzdaten im PKCS#7-Container umschließt, wird unter Verwendung der Verify-Document-Funktion des Konnektors validiert.
* `(26)` - Der Konnektor liefert das Ergebnis der Signaturvalidierung zurück.
* `(27)` - Sofern die Signaturvalidierung erfolgreich war, werden die FHIR-basierten E-Rezept-Nutzdaten aus dem PKCS#7-Container extrahiert.
* `(28)` - Die Medikamente werden auf dem vom Nutzer gewünschten Weg abgegeben und es wird eine entsprechende MedicationDispense-Struktur erzeugt.
* `(29)` - Die MedicationDispense-Struktur wird zusammen mit dem secret und dem Access-Token in einer $close-Nachricht an den FD_eRp gesandt, um die Dispensierung abzuschließen.
* `(30)` - Im Gegenzug liefert der FD_eRp eine signierte Quittung zurück.
* `(31)` - Das AVS führt eine geeignete Protokollierung durch.
* `(32)` - Der Status der Verarbeitung wird vom AVS and den eHealth-CardLink-Dienst übermittelt.
* `(33)` - Der eHealth-CardLink-Dienst überträgt den Status weiter an die App.
* `(34)` - Die App zeigt den Status schließlich dem Nutzer an.

## A.2 Nachrichten jenseits der gematik-Spezifikationen
Für die Kommunikation zwischen AVS und App müssen unter Berücksichtigung der entsprechenden FHIR-Profile 
* [KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/~overview)
* [KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview)
* [KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient)
* [KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview)
   
der Ressource [Medication](https://build.fhir.org/medication.html) und der zugehörigen "Technischen Anlage zur elektronischen Arzneimittelverordnung (E16A)" [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) der Kassenärztlichen Bundesvereinigung folgende Datenstrukturen und Nachrichten definiert werden:

* MedicationSummary
* AvailablePrescriptionList 
* SelectedPrescriptionList

### A.2.1 MedicationSummary
Die MedicationSummary-Datenstruktur enthält die Rezeptierdaten  einer elektronischen Verordnung in den verschiedenen Ausprägungen. 

Choice!

#### A.2.1.1 [KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview)

In diesem Profil werden die Rezeptierdaten einer Verordnung aus den Preis- und Produktverzeichnissen nach § 131 SGB V abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | 81 | 
| `Impfstoff` | boolean | 84 |
| `PZN` | string | 115 | 
| `Handelsname` | string | 116 |
| `Darreichungsform` | string | 103 | 
| `PackungsgroesseNachMenge` | string | 111 |
| `Einheit` | string | 112 | 
| `PackungsgroesseNachNBezeichnung` | string | 110 |

#### A.2.1.3 [KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient)

In diesem Profil werden die Rezeptierdaten einer Wirkstoffverordnung abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | 81 | 
| `Impfstoff` | boolean | 84 |
| `Wirkstoffnummer` | string | 118 | 
| `Wirkstoffname` | string | 119 | 
| `Wirkstaerke` | string | 120 | 
| `Wirkstaerkeneinheit` | string | 121 | 
| `Darreichungsform` | string | 104 (nicht 103!) | 
| `PackungsgroesseNachMenge` | string | 111 |
| `Einheit` | string | 112 | 
| `PackungsgroesseNachNBezeichnung` | string | 110 |

#### A.2.1.4 [KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview)

In diesem Profil werden die Rezeptierdaten einer Rezepturverordnung abgebildet.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | 81 | 
| `Impfstoff` | boolean | 84 |

| `Rezepturname` | string | 118 | 
| `Gesamtmenge` | string | 119 | 
| `Einheit` | string | 120 | 
| `Herstellungsanweisung` | string | 121 | 
| `Verpackung` | string | 104 (nicht 103!) | 

| `NameDesBestandteils` | string | 111 |
| `PZNDesBestandteils` | string | 112 | 
| `DarreichungsformDesBestandteils` | string | 110 |
| `MengeDesBestandteils` | string | 110 |
| `MengeUndEinheitDesBestandteils` | string | 110 |
| `DarreichungsformDesBestandteils` | string | 110 |



#### A.2.1.1 [KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/)

Die Freitext-Verordnung 


### A.2.2 AvailablePrescriptionList 

### A.2.3 SelectedPrescriptionList

