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

### A.1.3 - Phase 3 - Bereitstellen der E-Rezept-Informationen und Auswahl der zu dispensierenden Exemplare

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

Im Einzelnen sind dies die nachfolgend und im [YAML-Schema](https://github.com/eHealthCardLink/Spezifikation/blob/main/prescription-communication.yaml) bzw. der zugehörigen [html-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) beschriebenen Nachrichten in der Reihenfolge ihres Auftretens im oben spezifizierten Ablauf:

* A.2.1 - requestPrescriptionList
* A.2.2 - availablePrescriptionLists
* A.2.3 - selectedPrescriptionList
* A.2.4 - confirmSelection
  
### A.2.1 - requestPrescriptionList

Mit der **requestPrescriptionList**-Nachricht kann die App verfügbaren Verordnungen für ausgewählte oder alle verbundenen eGKs anfordern. Sofern eine oder mehrere **ICCSN**-Elemente (Integrated Circuit Card Serial Number) angegeben werden, werden die verfügbaren Verordnungen für genau die hierdurch eindeutig identifizierten eGKs angefordert. Wird kein **ICCSN**-Element angegeben, so werden die verfügbaren Verordnungen für alle verbundenen eGKs angefordert. 

Die technischen Details sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) 
näher beschrieben.

### A.2.2 - availablePrescriptionLists

Die **availablePrescriptionLists**-Nachricht besteht aus einer Folge von **availablePrescriptionList**-Elementen. 

Ein **availablePrescriptionList**-Element enthält ein **ICCSN**-Element und eine Folge von **prescriptionBundle**-Elementen (siehe Abschnitt A.3.10).

Die technischen Details sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) 
näher beschrieben.

### A.2.3 selectedPrescriptionList

Die **selectedPrescriptionList**-Nachricht spezifiziert, welche elektronischen Verordnungen genau dispensiert werden sollen und wie die verordneten Medikamente zugestellt werden sollen. 

Die **selectedPrescriptionList**-Nachricht ist an die Strukturen aus 
[gemSpec_DM_eRp](https://fachportal.gematik.de/fachportal-import/files/gemSpec_DM_eRp_V1.9.0.pdf) (Abschnitt 2.6.3) angelehnt und
umfasst die folgenden Elemente:

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `ICCSN` | string | Die ICCSN identifiziert die eGK für die elektronische Verordnungen eingelöst werden sollen. | 
| `PrescriptionIndexList` | prescriptionIndexList | Dieses Element spezifiziert, welche der verfügbaren elektronischen Verordnungen eingelöst werden sollen. Details sind in Abschnitt A.3.12 spezifiziert. |
| `supplyOptionsType` | string | Mit diesem Element kann die gewünschte Bereitstellungsart der verordneten Medikamente spezifiziert werden. Gemäß [gemSpec_DM_eRp](https://fachportal.gematik.de/fachportal-import/files/gemSpec_DM_eRp_V1.9.0.pdf) sind die Werte "onPremise", "shipment", "delivery" vorgesehen. |
| `name` | string | Name des Versicherten (optional).  |
| `address` | streetAddress | Adresse des Versicherten (optional). |
| `hint` | string | Hinweis, den der Versicherte mit angeben kann. (optional) |
| `text` | string | Freitext, den der Nutzer App-unterstützt eingeben kann. (optional) |
| `phone` | string | Telefonnummer des Versicherten.  |
| `mail` | string | E-Mail-Adresse des Versicherten.  |

Die technischen Details der **selectedPrescriptionList**-Nachricht sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

### A.2.4 - confirmSelection

Die **confirmSelection**-Nachricht dient der Bestätigung des Erhalts der vorher übermittelten **selectedPrescriptionList**-Nachricht.

Die technischen Details sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) 
näher beschrieben.

## A.3 In den Nachrichten enthaltene Datenelemente

In den oben spezifizierten Nachrichten sind die folgenden Elemente enthalten:

* A.3.1 - coverage
* A.3.2 - medication
* A.3.3 - organization
* A.3.4 - patient
* A.3.5 - pobAddress
* A.3.6 - person
* A.3.7 - practiceSupply
* A.3.8 - practitioner
* A.3.9 - practitionerRole
* A.3.10 - prescription
* A.3.11 - prescriptionBundle
* A.3.12 - prescriptionIndexList
* A.3.13 - streetAddress

#### A.3.1 - coverage

Das **coverage**-Element bildet die Informationen zum Krankenversicherungsverhältnis des Patienten ab.

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

### A.3.2 medication

Das **medication**-Element enthält die Rezeptierdaten einer elektronischen Verordnung in den verschiedenen vorgesehenen Ausprägungen:

* A.3.2.1 medicationPZN ([KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview))
* A.3.2.2 medicationIngredient ([KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient))
* A.3.2.3 medicationCompounding ([KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview))
* A.3.2.4 medicationFreeText ([KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/~overview))
   
Hierbei handelt es sich um entsprechende Profilierungen der Ressource [Medication](https://build.fhir.org/medication.html) gemäß der zugehörigen "Technischen Anlage zur elektronischen Arzneimittelverordnung (E16A)" [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) der Kassenärztlichen Bundesvereinigung (KBV). 

Technische Details der **medication**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

#### A.3.2.1 medicationPZN ([KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview))

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

#### A.3.2.2 medicationIngredient ([KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient))

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

#### A.3.2.3 medicationCompounding ([KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview))

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

#### A.3.2.4 medicationFreeText ([KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/))

In diesem Profil werden die Rezeptierdaten einer **freitextlichen Verordnung** abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | siehe oben | 
| `Impfstoff` | boolean | siehe oben |
| `Freitextverordnung` | string | Dieses Feld enthält den Text einer Freitextverordnung. | 
| `Darreichungsform` | string | siehe oben |

#### A.3.3 - organization

Das **organization**-Element enthält die Daten einer Organisation. 

Es ist in [KBV_PR_FOR_ORGANIZATION](http://hl7.org/fhir/R4/organization.html) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-03**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **organization**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `BSNR` | string | Dieses Feld enthält eine Betriebsstättennummer (BSNR) zur Identifikation einer Einrichtung. Im vertragsärztlichen Bereich ist das Feld eine Voraussetzung für die Abrechnung zwischen Leistungserbringern und Kostenträgern. Für Krankenhäuser ist die BSNR anzugeben. Im Rahmen von Krankenhausbehandlungen kann dieses Feld den Ort der Ausstellung abbilden. | 
| `IK-Nummer` | string | Dieses Feld enthält ein Institutionskennzeichen (IK), welches von der ARGE·IK vergeben wird und ein eindeutiges Merkmal zur Abrechnung mit den Trägern der Sozialversicherung ist. | 
| `KZV-AN` | string | Dieses Feld enthält eine Abrechnungsnummer der Kassenzahnärztlichen Vereinigung (KZV). | 
| `Standortnummer` | string | Dieses Feld enthält eine Standortnummer eines Krankenhauses. | 
| `Telematik-ID` | string | Dieses Feld enthält eine Telematik-ID der Einrichtung. | 
| `Name` | string | Dieses Feld enthält die Bezeichnung der Einrichtung (Praxis / Krankenhaus). | 
| `Adresse` | streetAdress | Siehe Abschnitt A.3.13. | 
| `Telefon` | string | Dieses Feld enthält die Telefonnummer. | 
| `Fax` | string | Dieses Feld enthält die Faxnummer. | 
| `E-Mail` | string | Dieses Feld enthält die E-Mail-Adresse der Einrichtung. Bei grenzüberschreitender Einlösung einer Arzneimittelverordnung ist diese zwingend anzugeben. | 

#### A.3.4 - patient

Das **patient**-Element bildet die Daten des Patienten ab. 

Es ist in [KBV_PR_FOR_PATIENT](https://fhir.kbv.de/StructureDefinition/KBV_PR_FOR_Patient) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-05**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **patient**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `GKV-VersichertenID` | string | Dieses Feld enthält die VersichertenID der gesetzlichen Krankenversicherung (unveränderlicher Teil der einheitlichen Krankenversicherungsnummer der GKV gemäß § 290 SGB V). | 
| `PKV-VersichertenID` | string | Dieses Feld enthält die VersichertenID der privaten Krankenversicherung (unveränderlicher Teil der einheitlichen Krankenversichertennummer gemäß § 290 SGB V). | 
| `KVK-Versichertennummer` | string | Dieses Feld enthält die Versichertennummer der Krankenversichertenkarte. | 
| `Person` | person | Dieses Element enthält die Informationen zur betreffenden Person. | 
| `Geburtsdatum` | date | Dieses Feld gibt das Geburtsdatum des Versicherten an. | 
| `Adresse` | streetAdress oder pobAdress | Entweder Straßenadresse (streetAdress) oder Postfachaddresse (pobAdress). | 

#### A.3.5 - pobAddress

Das **pobAddress**-Element enthält die Postfachadresse der betreffenden Person. 
Dieses Element ist folgendermaßen strukturiert:

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Land` | string | Dieses Feld enthält den Wohnsitzländercode (entsprechend [Gemeinsames Rundschreiben DEÜV](https://www.gkv-datenaustausch.de/arbeitgeber/deuev/gemeinsame_rundschreiben/gemeinsame_rundschreiben.jsp) [Anlage 08](https://www.gkv-datenaustausch.de/media/dokumente/arbeitgeber/deuev/rundschreiben_anlagen/03_Gem_RS_Anlage_8_Vers._8.00.pdf).| 
| `PLZ` | string | In diesem Feld kann die Postleitzahl angegeben werden. | 
| `Ort` | string | In diesem Feld kann der Ortsnamen angegeben werden. Mehrere Namensbestandteile sind durch Blank/Sonderzeichen getrennt. | 
| `Postfach` | string | In diesem Feld kann das Postfach angegeben werden. | 

#### A.3.6 - person

Das Element **person** wird in der Spezifikation der Elemente **patient** (Abschnitt A.3.4) und **practitioner** (Abschnitt A.3.8) genutzt und ist folgendermaßen strukturiert.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Vorname` | string | Dieses Feld enthält den Vornamen der Person; mehrere Vornamen sind durch Blank oder Bindestrich getrennt.| 
| `Name` | string | Dieses Feld enthält den Nachnamen des Person. | 
| `Titel` | string | Dieses Feld enthält den akademischen Grad der Person, z.B. „Dr. med.“, „Dr.rer.nat.“. | 
| `Namenszusatz` | string | Dieses Feld enthält den Namenszusatz als Bestandteil des Nachnamens der Person, z.B. „Freiherr“, „Gräfin“; mehrere Namenszusätze sind durch Blank getrennt. | 
| `Vorsatzwort` | string | Dieses Feld enthält das Vorsatzwort als Bestandteil des Nachnamens der Person, z.B. „von“, „von der“, „zu“ ; mehrere Vorsatzwörter sind durch Blank getrennt. | 

### A.3.7 - practiceSupply

Das **practiceSupply**-Element dient der Verschreibung von Sprechstundenbedarf gemäß [KBV_PR_ERP_PracticeSupply](https://simplifier.net/erezept/kbvprerppracticesupply). 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Datum` | date | Dieses Feld enthält das Ausstellungsdatum der Verordnung. | 
| `Anzahl` | integer | Dieses Feld enthält die Anzahl der verordneten Packungen. | 
| `Kostentraegertyp` | string | Dieses Feld gibt den Kostenträgertyp an. Gemäß [KBV_VS_FOR_Payor_type](https://simplifier.net/for/kbvvsforpayortype) und [KBV_CS_FOR_Payor_Type_KBV](https://simplifier.net/for/kbvcsforpayortypekbv) sind die folgenden  Werte vorgesehen: "GKV"	(gesetzliche Krankenversicherung), "PKV"	(private Krankenversicherung), "BG"	(Berufsgenossenschaft), "SEL"	(Selbstzahler), "SKT" (Sonstige Kostenträger), "UK" (Unfallkassen).| 
| `IK-Nummer` | string |Dieses Feld enthält das Institutionskennzeichen (IK) laut der elektronischen Gesundheitskarte (eGK). Siehe hierzu die Übertragungsregel gemäß [Technische Anlage zur Anlage 4a BMV-Ä](https://www.kbv.de/media/sp/04a_elektr._Gesundheitskarte_technische_Anlage.pdf) (Abschnitt 2.2).  | 
| `Name` | string | Dieses Feld enthält den Namen des Kostenträgers. Der korrekte Name ergibt sich aus den definierten Regeln gemäß [Technische Anlage zur Anlage 4a BMV-Ä](https://www.kbv.de/media/sp/04a_elektr._Gesundheitskarte_technische_Anlage.pdf) (Abschnitt 2.3). | 
   
Technische Details der **practiceSupply**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

#### A.3.8 - practitioner

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
| `Person` | person | siehe Abschnitt A.3.6  | 
| `VerantwortlichePerson` | practitioner | Dieses Element ist optional und kann dafür verwendet werden, wenn der verordnende Arzt nicht der verantwortliche Arzt ist.   | 

#### A.3.9 - practitionerRole

Das optionale **practitionerRole**-Element kann verwendet werden, um anzugeben, ob der verordnende Arzt eine weitere Rolle im Bereich der ambulanten spezialärztlichen Versorgung (ASV) innehat. 

Es ist in [KBV_PR_FOR_PRACTITIONERROLE](http://hl7.org/fhir/R4/practitionerrole.html) und "Technisches Handbuch Digitale Vordrucke" [KBV_ITA_VGEX_TECHNISCHES_HANDBUCH_DIMUS](https://update.kbv.de/ita-update/DigitaleMuster/KBV_ITA_VGEX_Technisches_Handbuch_DiMus.pdf) (**P4-02**) spezifiziert und nachfolgend näher erläutert.

Technische Details der **practitionerRole**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `ASV-TN` | string | Dieses Feld muss im Rahmen einer ambulanten spezialfachärztlichen Versorgung genutzt werden. Jedes ASV-Team erhält von der ASV-Servicestelle eine ASV-Teamnummer (ASV-TN). Mit ihr kennzeichnen ASV-Ärzte die Leistungen oder Verordnungen, die sie in der ASV durchführen. Die Teamnummer umfasst neun Ziffern und ist wie eine Betriebsstättennummer (BSNR) aufgebaut. Sie wird vergeben, sobald die Ärzte eine ASV-Berechtigung haben – zusätzlich zur BSNR und zur lebenslangen Arztnummer. | 

### A.3.10 - prescription

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
| `Coverage` | coverage | siehe Abschnitt A.3.1 |
| `Patient` | patient | siehe Abschnitt A.3.4 |
| `Practitioner` | practitioner | siehe Abschnitt A.3.8 | 

### A.3.11 - prescriptionBundle

Das **prescriptionBundle**-Element ist das für elektronische Verordnungen genutzte Bündelungselement, das von der [Bundle](https://hl7.org/fhir/R4/bundle.html)-Ressource abgeleitet wird und alle weiteren Datenelemente enthält. 

Es ist in [KBV_PR_ERP_Bundle](https://simplifier.net/packages/kbv.ita.erp/1.1.2/files/2212799) und der "Technischen Anlage zur elektronischen Arzneimittelverordnung (E16A)" [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) (Abschnitt 3.6.2) spezifiziert und nachfolgend näher erläutert. 

Technische Details der **prescriptionBundle**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `PrescriptionID` | string | Dieses Feld enthält den im Zeitraum von 11 Jahren eindeutigen Identifikator der elektronischen Verordnung gemäß [GEM_ERP_PR_PrescriptionId](https://simplifier.net/packages/de.gematik.erezept-workflow.r4/1.2.1/files/2030548) und [gemSpec_DM_eRp](https://fachportal.gematik.de/fachportal-import/files/gemSpec_DM_eRp_V1.9.0.pdf) (Abschnitt 2.2). |
| `Erstellungszeitpunkt` | date-time | Gibt den Zeitpunkt der Erstellung des E-Rezept-Bundles an. |
| `Status` | string | Enthält das Statuskennzeichen der elektronischen Verordnung. Die vorgesehenen Werte sind der Schlüsseltabelle [S_KBV_STATUSKENNZEICHEN](https://applications.kbv.de/S_KBV_STATUSKENNZEICHEN.xhtml) zu entnehmen. |
| `PKV-Tarif` | string | Enthält im Fall eines Privatrezeptes Informationen zum PKV-Tarif gemäß [KBV_EX_FOR_PKV_Tariff](https://simplifier.net/for/kbvexforpkvtariff). |
| `Krankenversicherung` | coverage | Enthält die Informationen zur Krankenversicherung. Details sind in Abschnitt A.3.1 spezifiziert. |
| `Patient` | patient | Enthält die Informationen zum Patienten. Details sind in Abschnitt A.3.4 spezifiziert. |
| `Arzt` | practitioner | Enthält die Informationen zum Aussteller der elektronischen Verordnung (z.B. Arzt). Details sind in Abschnitt A.3.8 spezifiziert. |
| `Pruefnummer` | string | Enthält die Prüfnummer des Praxisverwaltungssystems, mit dem die elektronische Verordnung erstellt wurde. |
| `Organisation` | organization | Enthält die Information zur ausstellenden Organisation. Details sind in Abschnitt A.3.3 spezifiziert. |
| `Verordnung` | prescription oder practiceSupply | Enthält weitere Informationen zur elektronischen Verordnung. Das **prescription**-Element ist in Abschnitt A.3.10 und das **practiceSupply**-Element ist in Abschnitt A.3.7 näher spezifiziert. | 
| `ASV-TN` | practitionerRole | Dieses Element kann verwendet werden, um anzugeben, ob der verordnende Arzt eine weitere Rolle im Bereich der ambulanten spezialärztlichen Versorgung (ASV) innehat. Details zum **practitionerRole**-Element sind in Abschnitt A.3.9 spezifiziert. | 
| `Arzneimittel` | medication | Dieses Element enthält die Informationen über das verordnete Arzneimittel. Details zum **medication**-Element sind in Abschnitt A.3.2 spezifiziert. | 

### A.3.12 - prescriptionIndexList

Das **prescriptionIndexList**-Element ist in der **selectedPrescriptionList**-Nachricht enthalten und spezifiziert, welche der verfügbaren elektronischen Verordnungen eingelöst werden sollen.

Das **prescriptionIndexList**-Element besteht aus einer Folge von **prescriptionID** Elementen, welche die einzulösenden Verordnungen identifizieren (siehe auch **prescriptionBundle** in Abschnitt A.3.11).

Technische Details der **prescriptionIndexList**-Datenstruktur sind in der [YAML-Dokumentation](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) näher beschrieben.

### A.3.13 - streetAddress

Das Element **streetAddress** ist folgendermaßen strukturiert.

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Land` | string | Dieses Feld enthält den Wohnsitzländercode (entsprechend [Gemeinsames Rundschreiben DEÜV](https://www.gkv-datenaustausch.de/arbeitgeber/deuev/gemeinsame_rundschreiben/gemeinsame_rundschreiben.jsp) [Anlage 08](https://www.gkv-datenaustausch.de/media/dokumente/arbeitgeber/deuev/rundschreiben_anlagen/03_Gem_RS_Anlage_8_Vers._8.00.pdf).| 
| `PLZ` | string | In diesem Feld kann die Postleitzahl angegeben werden. | 
| `Ort` | string | In diesem Feld kann der Ortsnamen angegeben werden. Mehrere Namensbestandteile sind durch Blank/Sonderzeichen getrennt. | 
| `Strasse` | string | In diesem Feld kann der Straßennamen angegeben werden. | 
| `Hausnummer` | string | In diesem Feld kann die Hausnummer angegeben werden. | 
| `Zusatz` | string | In diesem Feld kann der Anschriftenzusatz angegeben werden, z.B. Hinterhaus. | 


