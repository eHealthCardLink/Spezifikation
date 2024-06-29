<img align="right" width="80" height="80" src="https://github.com/eHealthCardLink/Spezifikation/blob/main/img/IOP-Logo.png"/><br/>

# Anhang A - Einlösen von E-Rezepten

<details>
  <summary><b>Inhaltsverzeichnis</b></summary>  
  Über dieses Dokument</br>
  Versionierung
  <ol>
    <li><b>A.1 Ablauf beim Einlösen von E-Rezepten</b></li>
       <ul>A.1.0 - eHealth-CardLink-Basisablauf</ul>
       <ul>A.1.1 - Phase 1 - Aufbau der Verbindung zum FD_eRp</ul>
       <ul>A.1.2 - Phase 2 - Auslesen der verfügbaren E-Rezepte aus FD_eRp</ul>
       <ul>A.1.3 - Phase 3 - Auslesen der verfügbaren E-Rezepte aus FD_eRp</ul>
       <ul>A.1.4 - Phase 4 - Verbindliche Zuweisung der zu dispensierenden E-Rezepte an Apotheke</ul>
       <ul>A.1.5 - Phase 5 - Signaturvalidierung, Dispensierung der E-Rezepte und Abschluss der Transaktion</ul>
    <li><b>A.2 Nachrichten jenseits der gematik-Spezifikationen</b></li>
      <ul>A.2.1 - availablePrescriptionLists</ul>
      <ul>A.2.2 - availablePrescriptionList</ul>
      <ul>A.2.3 - confirmSelection</ul>
      <ul>A.2.4 - medication</ul> 
        <ul>A.2.4.1 - medicationPZN (KBV_PR_ERP_Medication_PZN)</ul>
        <ul>A.2.4.2 - medicationIngredient (KBV_PR_ERP_Medication_Ingredient)</ul>
        <ul>A.2.4.3 - medicationCompounding (KBV_PR_ERP_Medication_Compounding)</ul>
        <ul>A.2.4.4 - medicationFreeText (KBV_PR_ERP_Medication_FreeText)</ul>
      <ul>A2.4.5 - practiceSupply</ul>
      <ul>A.2.6 - prescription</ul>
        <ul>A2.6.1 - coverage</ul>
        <ul>A2.6.2 - organisation</ul>
        <ul>A2.6.3 - patient</ul>
        <ul>A2.6.4 - practitioner</ul>
        <ul>A2.6.5 - practitionerRole</ul>
      <ul>A.2.7 - selectedPrescriptionList</ul>
  </ol>
</details>

## Über dieses Dokument

Dieses Dokument wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und spezifiziert
die Abläufe beim **Einlösen von elektronischen Verordnungen**. 
Diese Spezifikation ergänzt die [Basisspezifikation](https://github.com/eHealthCardLink/Spezifikation/blob/main/Spezifikation.md) für den eHealth-CardLink-Ablauf, der wiederum auf der entsprechenden 
Spezifikation [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) der gematik GmbH aufbaut.

Die Spezifikationen in diesem Dokument wird durch die folgende [asyncapi](https://www.asyncapi.com/)-Spezifikation ergänzt:

* [eHealth-CardLink Prescription Communication Interface](https://github.com/eHealthCardLink/Spezifikation/blob/main/prescription-communication.yaml) der eHealth-CardLink-Taskforce, die auch als [html](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) verfügbar ist. 

## Versionierung

| Version | Datum | Beschreibung der wesentlichen Änderungen | 
| --- | --- |  --- |
| `1.0.0 (RC)` | 25.06.2024 | Initiale Version | 

## A.1 Ablauf beim Einlösen von E-Rezepten

Der grundlegende Ablauf für das Einlösen von E-Rezepten ist im nachfolgenden Sequenzdiagramm dargestellt. 

![Ablauf zum Einlösen von E-Rezepten in einer Apotheke](https://github.com/eHealthCardLink/Spezifikation/blob/main/img/E-Rezept-Einl%C3%B6sen-Ablauf-mit-ApoShop.svg)

### A.1.0 - eHealth-CardLink-Basisablauf

* `(0)-(7)` - Bevor E-Rezepte in einer Apotheke mit dem eHealth-CardLink-Verfahren eingelöst werden können, müssen die notwendigen Schritte im [Basisablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/Spezifikation.md) durchgeführt werden.

### A.1.1 - Phase 1 - Aufbau der Verbindung zum FD_eRp

* `(8)` - In diesem Schritt muss für die spätere Kommunikation mit dem E-Rezept-Fachdienst (FD_eRp) beim IDP-Dienst eine Authentifizierung des AVS erfolgen, um ein verschlüsseltes ID-Token und Access-Token zu erhalten. Weitere Details zu diesem Ablauf finden sich in [gemSpec_IDP_FD](https://fachportal.gematik.de/fachportal-import/files/gemSpec_IDP_FD_V1.7.1.pdf) (Abschnitt 5.5). 
* `(9)` - Bevor fachliche Anfragen an den FD_eRp gestellt werden können, muss eine Verbindung mit der "Vertrauenswürdigen Ausführungsumgebung" (VAU) des FD_eRp aufgebaut werden. Details hierzu finden sich in [gemSpec_FD_eRp](https://fachportal.gematik.de/fachportal-import/files/gemSpec_FD_eRp_V2.1.0.pdf) (Abschnitt 5.8.5) und [gemSpec_Krypt](https://fachportal.gematik.de/fachportal-import/files/gemSpec_Krypt_V2.32.0.pdf) (Abschnitte 3.16 und 7).

### A.1.2 - Phase 2 - Auslesen der verfügbaren E-Rezepte aus FD_eRp

* `(10)` - Unter Verwendung des Prüfungsnachweises, der in Schritt 7 aus dem [Basisablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/Spezifikation.md) erhalten wurde, werden die verfügbaren E-Rezept-Tasks vom Apothekenverwaltungssystem (AVS) ermittelt.
* `(11)` - Die verfügbaren E-Rezept-Tasks werden in einem Bundle zurückgeliefert. 
* `(12)` - In diesem Schritt wird das erhaltene Bundle dekodiert.
* `(13)` - In einer Schleife über alle verfügbaren E-Rezepte wird unter Verwendung des Prüfungsnachweises das jeweilige E-Rezept angefordert.
* `(14)` - Das individuelle E-Rezept wird zusammen mit einem Geheimnis (secret) als PKCS#7-MIME-codierte MedicationReference vom FD_eRp zurückgeliefert.
* `(15)` - Jedes so erhaltene E-Rezept wird dekodiert und die E-Rezept-Informationen werden zur weiteren Verarbeitung extrahiert.

### A.1.3 - Phase 3 - Auslesen der verfügbaren E-Rezepte aus FD_eRp

* `(16)` - Die App fordert die Liste der verfügbaren E-Rezepte beim eHealth-CardLink-Dienst an (**requestPrescriptionList**).
* `(17)` - Der eHealth-CardLink-Dienst schickt die Nachricht (**requestPrescriptionList**) weiter zum AVS.
* `(18)` - Die verfügbaren E-Rezept-Informationen werden in einer Liste (**availablePrescriptionLists**) zusammengefasst und über die in Schritt 7.1 etablierte Websocket-Verbindung an den eHealth-CardLink-Dienst übertragen.
* `(19)` - Der eHealth-CardLink-Dienst reicht die Liste mit den E-Rezept-Informationen (**availablePrescriptionLists**) weiter an die App.
* `(20)` - Die App zeigt dem Nutzer die Liste der verfügbaren E-Rezepte an.
* `(21)` - Der Nutzer wählt die zu dispensierenden E-Rezepte aus und erfasst ggf. weitere Daten zur Belieferung des E-Rezepts.
* `(22)` - Sofern ein Apotheken-Webshop ("ApoShop") existiert, kann dieser nun die weitere Steuerung der Abläufe übernehmen, so dass der Nutzer bei Bedarf weitere Waren (z.B. OTC-Artikel) auswählen und die Bezahlung durchführen kann.
* `(23)` - Sofern kein ApoShop existiert, werden hier die vom Nutzer ausgewählten E-Rezepte (**selectedPrescriptionList**) von der App an den eHealth-CardLink-Dienst übertragen.
* `(24)` - Die Liste mit den ausgewählten E-Rezepten (**selectedPrescriptionList**) wird vom eHealth-CardLink-Dienst ans AVS übertragen.
* `(25)` - Der Empfang der Nachricht wird vom AVS mit der Nachricht (**confirmPrescriptonList**) quittiert.
* `(26)` - Die (**confirmPrescriptonList**) Nachricht wird an die App weitergeleitet und signalisiert dort, dass der Ablauf für die App beendet ist.

### A.1.4 - Phase 4 - Verbindliche Zuweisung der zu dispensierenden E-Rezepte an Apotheke

* `(27)` - Sofern in Schritt (22) die Steuerung durch den ApoShop übernommen wurde, werden in diesem Schritt die zur Dispensierung vorgesehenen E-Rezepte in der (**selectedPrescriptionList**) Nachricht an das AVS geschickt.
* `(28)` - Analog zu Schritt (25) wird hier der Empfang der Nachricht vom AVS mit der Nachricht (**confirmPrescriptonList**) quittiert.
* `(29)` - In diesem Schritt wird das E-Rezept mit einer **$accept**-Nachricht verbindlich bei der Apotheke eingereicht.
* `(30)` - Der FD_eRp liefert ein Bundle mit einer Task und einen PKCS#7-Container mit dem signierten E-Rezept zurück.
* `(31)` - Das erhaltene Bundle wird dekodiert und das enthaltene secret wird gespeichert.

### A.1.5 - Phase 5 - Signaturvalidierung, Dispensierung der E-Rezepte und Abschluss der Transaktion
  
* `(32)` - Sofern das AVS in einer verteilten Form realisiert ist und ein Teil in einem Rechenzentrum und ein anderer Teil in einer Vor-Ort-Apotheke betrieben wird, können hier weitere optionale Abläufe zur AVS-internen Kommunikation auftreten. 
* `(33)` - Die qualifizierte elektronische Signatur, die die E-Rezept-Nutzdaten im PKCS#7-Container umschließt, wird unter Verwendung der **VerifyDocument**-Funktion des Konnektors validiert.
* `(34)` - Der Konnektor liefert das Ergebnis der Signaturvalidierung mit **VerifyDocumentResponse** zurück.
* `(35)` - Sofern die Signaturvalidierung erfolgreich war, werden die FHIR-basierten E-Rezept-Nutzdaten aus dem PKCS#7-Container extrahiert.
* `(36)` - Die Medikamente werden auf dem vom Nutzer gewünschten Weg abgegeben und es wird eine entsprechende MedicationDispense-Struktur erzeugt.
* `(37)` - Die MedicationDispense-Struktur wird zusammen mit dem secret und dem Access-Token in einer **$close**-Nachricht an den FD_eRp gesandt, um die Dispensierung abzuschließen.
* `(38)` - Im Gegenzug liefert der FD_eRp eine signierte Quittung zurück.
* `(39)` - Das AVS führt eine geeignete Protokollierung durch.
* `(40)` - Im letzten Schritt werden hier die im E-Rezept verordneten Medikamente an den Nutzer per Boten oder Paketversand geliefert bzw. in einer Vor-Ort-Apotheke übergeben.

## A.2 Nachrichten jenseits der gematik-Spezifikationen 

Zur Realisierung des oben beschriebenen Ablaufs zum Einlösen von elektronischen Verordnungen
sind eine Reihe von Nachrichten jenseits der einschlägigen gematik-Spezifikationen nötig.

Im Einzelnen sind dies die nachfolgend und im [YAML-Schema](https://github.com/eHealthCardLink/Spezifikation/blob/main/prescription-communication.yaml) bzw. der zugehörigen [html-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) beschriebenen Nachrichten:

* A.2.1 - availablePrescriptionLists
* A.2.2 - availablePrescriptionLists
* A.2.3 - confirmSelection
* A.2.4 - medication
  * A.2.4.1 - medicationPZN (KBV_PR_ERP_Medication_PZN)
  * A.2.4.2 - medicationIngredient (KBV_PR_ERP_Medication_Ingredient)
  * A.2.4.3 - medicationCompounding (KBV_PR_ERP_Medication_Compounding)
  * A.2.4.4 - medicationFreeText (KBV_PR_ERP_Medication_FreeText)
* A2.5 - practiceSupply
* A.2.6 - prescription
  * A2.6.1 - coverage
  * A2.6.2 - organisation
  * A2.6.3 - patient
  * A2.6.4 - practitioner
  * A2.6.5 - practitionerRole
* A.2.7 - selectedPrescriptionList

### A.2.1 - availablePrescriptionLists

Die **availablePrescriptionLists**-Nachricht besteht aus einer Folge von **availablePrescriptionList**-Elementen.
Diese sind in Abschnitt A.2.2 und in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) 
näher beschrieben.

### A.2.2 - availablePrescriptionList

Ein **availablePrescriptionList**-Element enthält die "Integrated Circuit Card Serial Number" (**ICCSN**) und eine Folge von **medication**-Elementen.
Diese sind in Abschnitt A.2.3 und in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) 
näher beschrieben.

### A.2.3 - confirmSelection

Die **confirmSelection**-Nachricht dient der Bestätigung des Erhalts der vorher übermittelten **selectedPrescriptionList**-Nachricht.
Die technischen Details sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) 
näher beschrieben.

### A.2.4 medication
Das **medication**-Element enthält die Rezeptierdaten einer elektronischen Verordnung in den verschiedenen vorgesehenen Ausprägungen:

* A.2.4.1 medicationPZN ([KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview))
* A.2.4.2 medicationIngredient ([KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient))
* A.2.4.3 medicationCompounding ([KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview))
* A.2.4.4 medicationFreeText ([KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/~overview))
   
Hierbei handelt es sich um entsprechende Profilierungen der Ressource [Medication](https://build.fhir.org/medication.html) gemäß der zugehörigen "Technischen Anlage zur elektronischen Arzneimittelverordnung (E16A)" [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) der Kassenärztlichen Bundesvereinigung (KBV). 

Technische Details der **medication**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

#### A.2.4.1 medicationPZN ([KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview))

In diesem Profil werden die Rezeptierdaten einer Verordnung aus den Preis- und Produktverzeichnissen nach § 131 SGB V abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | Dieses Feld enthält die Kennzeichnung der Verordnungskategorie, bspw. für ein BtM-Rezept. Der in Abschnitt 6.1 von [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) definierte Standardwert ist "00".| 
| `Impfstoff` | boolean | Dieses Feld enthält die Kennzeichnung, ob es sich bei der Verordnung um Impfstoff handelt. Der Standardwert ist "false" und gibt an, dass es sich nicht um einen Impfstoff handelt. "true" gibt an, dass es sich um einen Impfstoff handelt. |
| `Normgroesse` | string | Enthält den Code der Normgröße der Arzneimittelverordnung. Die Schlüsseltabelle für die Anzeige der codierten Werte wird von der KBV gepflegt und ist unter [S_KBV_NORMGROESSE](https://applications.kbv.de/S_KBV_NORMGROESSE.xhtml) verfügbar. | 
| `PZN` | string | Dieses Feld enthält die Pharmazentralnummer (PZN), die von der Informationsstelle für Arzneispezialitäten IFA, Frankfurt produktbezogen vergeben wird und für die gesetzlichen Krankenkassen gemäß Vereinbarungen nach § 131 SGB V mit der Pharmazeutischen Industrie und nach § 300 SGB V mit dem Deutschen Apothekerverband vereinbart ist. Die Angaben Handelsname, Darreichungsform, Packungsgröße usw. entstammen den Preis- und Produktangaben nach § 131 Abs. 4 SGB V. | 
| `Handelsname` | string | Dieses Feld enthält den Handelsnamen des verordneten Präparates, welcher aus der PZN abgeleitet wird. |
| `Darreichungsform` | string | Dieses Feld enthält die Angabe der Darreichungsform entsprechend der Daten nach § 131 Abs. 4 SGB V und ist immer zu verwenden, wenn ein Fertigarzneimittel gemäß Preis- und Produktverzeichnis verordnet wird. Die Schlüsseltabelle für die Anzeige der codierten Werte wird von der KBV gepflegt und ist unter [S_KBV_DARREICHUNGSFORM](https://applications.kbv.de/S_KBV_DARREICHUNGSFORM.xhtml) verfügbar.| 
| `PackungsgroesseNachMenge` | string | Dieses Feld enthält die Packungsgröße (z.B. 100) und tritt nur in Verbindung mit „Einheit“ auf (z.B. 100 Stück). | 
| `Einheit` | string | Dieses Feld enthält die Einheit (z.B. Stück) und tritt nur in Verbindung mit „Packungsgröße nach abgeteilter Menge“ auf (z.B. 100 Stück). | 
| `PackungsgroesseNachNBezeichnung` | string | Dieses Feld enthält die Normgröße der therapiegerechten Packung (z.B. N1). |

#### A.2.4.2 medicationIngredient ([KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient))

In diesem Profil werden die Rezeptierdaten einer **Wirkstoffverordnung** abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | siehe oben | 
| `Impfstoff` | boolean | siehe oben |
| `Normgroesse` | string | siehe oben |
| `Darreichungsform` | string | Dieses Feld enthält die Darreichungsform als Freitext und kann verwendet werden, wenn es sich nicht um ein Fertigarzneimittel handelt. | 
| `PackungsgroesseNachMenge` | string | siehe oben |
| `Einheit` | string | siehe oben | 
| `PackungsgroesseNachNBezeichnung` | string | siehe oben |
| `BestandteilWirkstoffverordnung` | array | Eine Wirkstoffverordnung kann eine oder mehrere Bestandteile umfassen. | 

Das Element `BestandteilWirkstoffverordnung` enthält die folgenden Elemente:

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Wirkstoffnummer` | string | Dieses Feld enthält eine ASK-Nummer (Arzneimittelstoffkatalog-Nummer). | 
| `Wirkstoffname` | string | Dieses Feld enthält einen Wirkstoffnamen für ein Wirkstoff- bzw. sonstiges Fertigarzneimittel oder ein Produkt, welches nicht nach § 131 Abs. 4 SGB V gelistet ist. | 
| `Wirkstaerke` | string | Dieses Feld enthält eine Angabe der Wirkstärke. Diese ermittelt sich durch die Angabe von Wirkstoffmenge / Bezugsgrößenmenge. Die zugehörige Einheit ist im Feld "Wirkstärkeneinheit" anzugeben. | 
| `Wirkstaerkeneinheit` | string | Dieses Feld enthält die Einheit der Wirkstärke (bspw. mg/ml). | 

#### A.2.4.3 medicationCompounding ([KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview))

In diesem Profil werden die Rezeptierdaten einer **Rezepturverordnung** abgebildet.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | siehe oben | 
| `Impfstoff` | boolean | siehe oben |
| `Herstellungsanweisung` | string | Dieses Feld enthält Anweisungen bzgl. der Herstellung der Rezeptur (Subscriptio). | 
| `Verpackung` | string | Dieses Feld enthält Angaben zur Verpackung der Rezeptur, z.B. Transportbehältnisse, und Applikationshilfen. | 
| `Rezepturname` | string | Dieses Feld enthält eine Bezeichnung der Rezeptur (z.B. gemäß Deutschem Arzneibuch: Zinkpaste DAB). | 
| `Darreichungsform` | string | Dieses Feld enthält die Darreichungsform der Rezeptverordnung als Freitext und kann verwendet werden, wenn es sich bei dem Bestandteil der Rezeptur nicht um ein Fertigarzneimittel handelt. |
| `Gesamtmenge` | string | Dieses Feld enthält die Gesamtmenge der Rezeptur (ohne die Einheit). | 
| `Einheit` | string | Dieses Feld enthält die Einheit der Gesamtmenge der Rezeptur. | 
| `BestandteilRezepturVerordnung` | array | Eine Rezepturverordnung kann eine oder mehrere Bestandteile umfassen. | 

Das Element `BestandteilRezepturverordnung` enthält die folgenden Elemente:

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Darreichungsform` | string | Dieses Feld enthält die Darreichungsform des Bestandteils als Freitext und kann verwendet werden, wenn es sich bei dem Bestandteil der Rezeptur nicht um ein Fertigarzneimittel handelt. |
| `Name` | string | Dieses Feld enthält die namentliche Bezeichnung des Bestandteils der Rezeptur. Wenn die PZN des Bestandteils vorhanden ist, dann ist der hinter der PZN liegende Name anzugeben. |
| `PZN` | string | Dieses Feld enthält die namentliche Bezeichnung des Bestandteils der Rezeptur. Wenn die PZN des Bestandteils vorhanden ist, dann ist der hinter der PZN liegende Name anzugeben. | 
| `Menge` | string | Dieses Feld enthält die Menge des Bestandteils z. B. 100. |
| `Einheit` | string | Dieses Feld enthält die Einheit des Bestandteils z.B. mg. |
| `MengeUndEinheit` | string | Dieses Feld enthält eine freitextliche Angabe zur Menge und Einheit des Bestandteils und kann insbesondere für klassische lateinische Angaben z.B. „ad 100,0“ oder „quantum satis“ genutzt werden. |

#### A.2.4.4 medicationFreeText ([KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/))

In diesem Profil werden die Rezeptierdaten einer **freitextlichen Verordnung** abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | siehe oben | 
| `Impfstoff` | boolean | siehe oben |
| `Freitextverordnung` | string | Dieses Feld enthält den Text einer Freitextverordnung. | 
| `Darreichungsform` | string | siehe oben |

### A2.5 - practiceSupply

Das **practiceSupply**-Element dient der Verschreibung von Sprechstundenbedarf gemäß [KBV_PR_ERP_PRACTICESUPPLY](https://simplifier.net/erezept/kbvprerppracticesupply). 
   
Technische Details der **practiceSupply**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

### A.2.6 - prescription

Das **prescription**-Element bildet die fachlich und medizinisch relevanten Bestandteile einer Arzneimittelverordnung ab.

Es ist in [KBV_PR_ERP_Prescription](https://simplifier.net/erezept/kbvprerpprescription) und der "Technischen Anlage zur elektronischen Arzneimittelverordnung (E16A)" [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) (**P36-26**) spezifiziert und nachfolgend näher erläutert. 

Technische Details der **prescription**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Ausstellungsdatum` | date | Dieses Feld enthält das Ausstellungsdatum der Verordnung. | 
| `Noctu` | boolean | Dieses Feld enthält die Kennzeichnung, ob diese Verordnung auch während der allgemeinen Ladenschlusszeiten beliefert werden soll, verbunden mit der Erhebung der Noctu-Gebühr (Notdienstgebühr) zu Lasten der Krankenkasse gemäß Arzneimittelpreisverordnung. |
| `BVG` | boolean | Dieses Feld enthält die Kennzeichnung, ob diese Verordnung für Anspruchsberechtigte nach dem Bundesentschädigungsgesetz (BEG) oder für Anspruchsberechtigte nach dem Bundesversorgungsgesetz (BVG) erfolgt. |
| `Zuzahlungsstatus` | string | In diesem Feld wird der Zuzahlungsstatus für die Verordnung angegeben. Gemäß [KBV_CS_FOR_StatusCoPayment](https://simplifier.net/packages/kbv.ita.for/1.1.0/files/720086) sind folgende Möglichkeiten vorgesehen: 0 (von Zuzahlungspflicht nicht befreit / gebührenpflichtig), 1	(von Zuzahlungspflicht befreit / gebührenfrei), 2 (künstliche Befruchtung (Regelung nach § 27a SGB V))|
| `AutIdem` | boolean | Dieses Feld enthält die Angabe, ob das Arzneimittel austauschbar ist oder nicht. Wenn ein Austausch in der Apotheke zulässig ist, wird dieses Feld auf true gesetzt. |
| `Abgabehinweis` | string | Dieses Feld enthält über die Dosierung hinausgehende / sonstige Abgabehinweise an die Apotheke. |
| `Anzahl` | integer | Dieses Feld enthält die Anzahl der verordneten Packungen. |
| `Dosierung` | boolean | Dieses Feld enthält ein Kennzeichen zur Dosierung und beschreibt, ob eine Dosieranweisung übermittelt oder ein Medikationsplan mitgegeben wird. |
| `Dosieranweisung` | string | Dieses Feld enthält eine Dosieranweisung. |
| `Gebrauchsanweisung` | string | Dieses Feld enthält die Gebrauchsanweisung der Rezeptur. |
| `Unfallkennzeichen` | string | Dieses Feld enthält die Information, in welchem Zusammenhang die Verordnung ausgestellt wurde, z.B. Unfall. Die möglichen Werte sind in [KBV_VS_ERP_Accident_Type](https://simplifier.net/erezept/kbvvserpaccidenttype) spezifiziert. Hierbei sind folgende Fälle vorgesehen: 1	(Unfall), 2	(Arbeitsunfall (Berufsgenossenschaft/Unfallkasse)), 4	(Berufskrankheit (Berufsgenossenschaft/Unfallkasse)) |
| `Unfalltag` | date | Tag des Unfalls |
| `Unfallbetrieb` | string | Unfallbetrieb |
| `Mehrfachverordnung` | boolean | Dieses Feld enthält die Kennzeichnung, ob es sich bei der Verordnung um eine Mehrfachverordnung (MFV) handelt. |
| `MFV-ID` | string | Dieses Feld enthält eine eineindeutige ID, welche über alle Teilverordnungen einer Mehrfachverordnung identisch ist. |
| `MFV-Zaehler` | integer | Dieses Feld enthält die Angabe, um die wievielte Teilverordnung einer Mehrfachverordnung (Serie) es sich handelt. Beispiel: "2" in "2 von 4" |
| `MFV-Nenner` | integer | Dieses Feld enthält die Angabe der Länge dieser Serie, d.h. die Gesamtanzahl der Teilverordnungen der Mehrfachverordnung. Beispiel: "4" in "2 von 4" |
| `MFV-Beginn` | date | Dieses Feld enthält das Datum, ab dem die Teilverordnung der Mehrfachverordnung eingelöst werden kann. |
| `MFV-Ende` | date | Dieses Feld enthält das Datum des letzten Einlösetages der Teilverordnung der Mehrfachverordnung. Von der ausstellenden Person kann eine von der Arzneimittelverschreibungsverordnung (AMVV) abweichende Einlösefrist angegeben werden. |
| `Coverage` | coverage | siehe Abschnitt A.2.6.1 |
| `Patient` | patient | siehe Abschnitt A.2.6.3 |
| `Practitioner` | practitioner | siehe Abschnitt A.2.6.4 | 

#### A2.6.1 - coverage

Das **coverage**-Element bildet die fachlich und medizinisch relevanten Bestandteile einer Arzneimittelverordnung ab.

Es ist in [KBV_PR_FOR_COVERAGE](https://simplifier.net/packages/kbv.ita.for/1.1.0/files/720092) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-04**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **coverage**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kostentraegertyp` | string | Dieses Feld gibt den Kostenträgertyp an. Gemäß [KBV_VS_FOR_Payor_type](https://simplifier.net/for/kbvvsforpayortype) und [KBV_CS_FOR_Payor_Type_KBV](https://simplifier.net/for/kbvcsforpayortypekbv) sind die folgenden  Werte vorgesehen: "GKV"	(gesetzliche Krankenversicherung), "PKV"	(private Krankenversicherung), "BG"	(Berufsgenossenschaft), "SEL"	(Selbstzahler), "SKT" (Sonstige Kostenträger), "UK" (Unfallkassen).| 
| `IK-Krankenkasse` | string | Dieses Feld enthält das Institutionskennzeichen (IK) der zuständigen Krankenkasse z.B. laut elektronischer Ge-sundheitskarte (eGK). | 
| `IK-Kostentraeger` | string | Dieses Feld enthält das Institutionskennzeichen (IK) des Kostenträgers und wird verwendet, wenn der Kostenträger nicht die zuständige Krankenkasse ist, bspw. eine Berufsgenossenschaft (BG) oder eine Unfallkasse (UK). | 
| `Kostentraeger` | string | Name des Kostenträgers | 
| `WOP` | string | Dieses Feld enthält das Wohnortkennzeichen entsprechend des Wohnortprinzips (WOP) für Honorarvereinbarungen (BMV-Ä Anlage 21). Die vorgesehenen Werte sind der Schlüsseltabelle [S_ITA_WOP](https://applications.kbv.de/S_ITA_WOP.xhtml) zu entnehmen.| 
| `Versichertenstatus` | string | Dieses Element enthält Angaben zum Versichertenstatus. Gemäß [Leitfaden Basis DE (STU3)](https://ig.fhir.de/basisprofile-de/0.2.30/Versichertenstatus2.html) sind folgende Werte vorgesehen: "1"	(Mitglieder), "3"	(Familienangehörige), "5" (Rentner). | 
| `BesonderePersonengruppe` | string | Dieses Feld enthält die besondere Personengruppe, zu der der Versicherte gehört (§ 264 SGB V). Die entsprechenden Werte sind der Schlüsseltabelle [S_KBV_PERSONENGRUPPE](https://applications.kbv.de/S_KBV_PERSONENGRUPPE.xhtml) zu entnehmen. | 
| `DMP-KZ` | string | Dieses Feld enthält das Disease-Management-Programm (DMP), in dem der Versicherte eingeschrieben ist (§ 284 Abs. 1 Satz 1 Nr. 14 SGB V). Die vorgesehenen Werte sind der Schlüsseltabelle [S_KBV_DMP](https://applications.kbv.de/S_KBV_DMP_V1.06.xhtml) zu entnehmen. | 
| `Versicherungsschutz-Ende` | date | In diesem Feld kann das Datum des Endes des Versicherungsschutzes angegeben werden, wenn die Datumsangabe auf der Versichertenkarte gespeichert ist und ausgelesen wurde. | 
  
#### A2.6.2 - organization

Das **organization**-Element bildet die Daten des Patienten ab. 

Es ist in [KBV_PR_FOR_ORGANIZATION](http://hl7.org/fhir/R4/organization.html) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-03**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **organization**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Identifikator` | string | 61 | 
| `BSNR` | string | 61a | 
| `IK-Nummer` | string | 61b | 
| `KZV-AN` | string | 61c | 
| `Standortnummer` | string | 61d | 
| `Telematik-ID` | string | 61 | 
| `Name` | string | 62 | 



  
#### A2.6.3 - patient

Das **patient**-Element bildet die Daten des Patienten ab. 

Es ist in [KBV_PR_FOR_PATIENT](https://fhir.kbv.de/StructureDefinition/KBV_PR_FOR_Patient) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-05**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **patient**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Identifikator` | string | Dieses Feld enthält den Identifikator der Person, z.B. die Krankenversicherungsnummer der GKV oder PKV. | 
| `GKV-VersichertenID` | string | Dieses Feld enthält die VersichertenID der gesetzlichen Krankenversicherung (unveränderlicher Teil der einheitlichen Krankenversicherungsnummer der GKV gemäß § 290 SGB V). | 
| `PKV-VersichertenID` | string | Dieses Feld enthält die VersichertenID der privaten Krankenversicherung (unveränderlicher Teil der einheitlichen Krankenversichertennummer gemäß § 290 SGB V). | 
| `KVK-Versichertennummer` | string | Dieses Feld enthält die Versichertennummer der Krankenversichertenkarte. | 
| `Person` | person | Dieses Element enthält die Informationen zur betreffenden Person. | 
| `Geburtsdatum` | date | Dieses Feld gibt das Geburtsdatum des Versicherten an. | 
| `Adresse` | strassenaddresse oder postfachadresse | Entweder Straßenadresse oder Postfachaddresse. | 

Das Element **strassenadresse** ist folgendermaßen strukturiert.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Land` | string | Dieses Feld enthält den Wohnsitzländercode (entsprechend Gemeinsames Rundschreiben DEÜV Anlage 08). | 
| `PLZ` | string | In diesem Feld kann die Postleitzahl angegeben werden. | 
| `Ort` | string | In diesem Feld kann der Ortsnamen angegeben werden. Mehrere Namensbestandteile sind durch Blank/Sonderzeichen getrennt. | 
| `Strasse` | string | In diesem Feld kann der Straßennamen angegeben werden. | 
| `Hausnummer` | string | In diesem Feld kann die Hausnummer angegeben werden. | 
| `Zusatz` | string | In diesem Feld kann der Anschriftenzusatz angegeben werden, z.B. Hinterhaus. | 

Das Element **postfachadresse** ist folgendermaßen strukturiert.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Land` | string | siehe oben | 
| `PLZ` | string | siehe oben | 
| `Ort` | string | siehe oben | 
| `Postfach` | string | In diesem Feld kann das Postfach angegeben werden. | 

#### A2.6.x - person

Das Element **person** wird in der Spezifikation der Elemente **patient** (Abschnitt A.2.6.x) und **practitioner** (Abschnitt A.2.6.x) genutzt und ist folgendermaßen strukturiert.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Vorname` | string | Dieses Feld enthält den Vornamen der Person; mehrere Vornamen sind durch Blank oder Bindestrich getrennt.| 
| `Name` | string | Dieses Feld enthält den Nachnamen des Person. | 
| `Titel` | string | Dieses Feld enthält den akademischen Grad der Person, z.B. „Dr. med.“, „Dr.rer.nat.“. | 
| `Namenszusatz` | string | Dieses Feld enthält den Namenszusatz als Bestandteil des Nachnamens der Person, z.B. „Freiherr“, „Gräfin“; mehrere Namenszusätze sind durch Blank getrennt. | 
| `Vorsatzwort` | string | Dieses Feld enthält das Vorsatzwort als Bestandteil des Nachnamens der Person, z.B. „von“, „von der“, „zu“ ; mehrere Vorsatzwörter sind durch Blank getrennt. | 

#### A2.6.4 - practitioner

Das **practitioner**-Element bildet die Daten des verordnenden Leistungserbringer (z.B. Arzt) ab. 

Es ist in [KBV_PR_FOR_Practitioner](https://simplifier.net/for/kbvprforpractitioner) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-01**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **practitioner**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Typ` | string | Dieses Feld enthält einen Typ zur Kennzeichnung der verschreibenden Person, z.B. Arzt, Arzt in Weiterbildung. Die vorgesehenen Werte sind der Schlüsseltabelle [KBV_CS_FOR_Qualification_Type](https://simplifier.net/for/kbvcsforqualificationtype) zu entnehmen. | 
| `Berufsbezeichnung` | string | Dieses Feld enthält eine Freitextangabe zur Berufsbezeichnung, z. B. Facharzt für Allgemeinmedizin, Praktischer Arzt, Hebamme. | 
| `ASV-FGN` | string | Dieses Feld enthält die ASV-Fachgruppennummer (ASV-FGN) gemäß der Vereinbarung über ambulante spezialärztliche Versorgung (ASV) (ASV-AV) § 9 Absatz 5. Diese ist gemäß der ASV-AV von Krankenhausärzten an Stelle der Arztnummer anzugeben. | 
| `Arztnummer` | string | Dieses Feld enthält als Identifikator der Person eine Arztnummer (Lebenslange Arztnummer LANR). | 
| `Zahnarztnummer` | string | Dieses Feld enthält als Identifikator der Person, eine Zahnarztnummer (ZANR). | 
| `Telematik-ID` | string | Dieses Feld enthält als Identifikator der Person eine Telematik-ID. | 
| `Person` | person | siehe Abschnitt A.2.6.x  | 
| `VerantwortlichePerson` | practitioner | Dieses Element ist optional.   | 

#### A2.6.5 - practitionerRole

Das optionale **practitionerRole**-Element kann verwendet werden, um anzugeben, ob der verordnende Arzt eine weitere Rolle innehat. 

Es ist in [KBV_PR_FOR_PRACTITIONERROLE](http://hl7.org/fhir/R4/practitionerrole.html) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-02**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **practitionerRole**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `ASV-TN` | string | Dieses Feld muss im Rahmen einer ambulanten spezialfachärztlichen Versorgung (ASV) genutzt werden. Jedes ASV-Team erhält von der ASV-Servicestelle eine ASV-Teamnummer (ASV-TN). Mit ihr kennzeichnen ASV-Ärzte die Leistungen oder Verordnungen, die sie in der ASV durchführen. Die Teamnummer umfasst neun Ziffern und ist wie eine Betriebsstättennummer (BSNR) aufgebaut. Sie wird vergeben, sobald die Ärzte eine ASV-Berechtigung haben – zusätzlich zur BSNR und zur lebenslangen Arztnummer. | 

### A.2.7 selectedPrescriptionList

Die **selectedPrescriptionList**-Nachricht spezifiziert, welche elektronischen Verordnungen genau dispensiert werden sollen. 

Technische Details der **selectedPrescriptionList**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.
