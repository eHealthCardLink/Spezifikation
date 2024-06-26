<img align="right" width="80" height="80" src="https://github.com/eHealthCardLink/Spezifikation/blob/main/img/IOP-Logo.png"/><br/>

# Anhang A - Einlösen von E-Rezepten

<details>
  <summary><b>Inhaltsverzeichnis</b></summary>  
  Über dieses Dokument</br>
  Versionierung
  <ol>
    <li><b>A.1 Ablauf beim Einlösen von E-Rezepten</b></li>
       <ul>A.1.0 - CardLink-Basisablauf</ul>
       <ul>A.1.1 - Phase 1 - Aufbau der Verbindung zum FD_eRp</ul>
       <ul>A.1.2 - Phase 2 - Auslesen der verfügbaren E-Rezepte aus FD_eRp</ul>
       <ul>A.1.3 - Phase 3 - Auslesen der verfügbaren E-Rezepte aus FD_eRp</ul>
       <ul>A.1.4 - Phase 4 - Verbindliche Zuweisung der zu dispensierenden E-Rezepte an Apotheke</ul>
       <ul>A.1.5 - Phase 5 - Signaturvalidierung, Dispensierung der E-Rezepte und Abschluss der Transaktion</ul>
    <li><b>A.2 Nachrichten jenseits der gematik-Spezifikationen</b></li>
      <ul>A.2.1 availablePrescriptionLists</ul>
      <ul>A.2.1 availablePrescriptionLists</ul>
      <ul>A.2.2 MedicationSummary</ul> 
        <ul>A.2.1.1 KBV_PR_ERP_Medication_PZN</ul>
        <ul>A.2.1.2 KBV_PR_ERP_Medication_Ingredient</ul>
        <ul>A.2.1.3 KBV_PR_ERP_Medication_Compounding</ul>
        <ul>A.2.1.4 KBV_PR_ERP_Medication_FreeText</ul>
     
      <ul>A.2.3 selectedPrescriptionList</ul>
  </ol>
</details>

## Über dieses Dokument

Dieses Dokument wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und spezifiziert
die Abläufe beim **Einlösen von elektronischen Verordnungen**. 
Diese Spezifikation ergänzt die [Basisspezifikation](https://github.com/eHealthCardLink/Spezifikation/blob/main/Spezifikation.md) für den eHealth-CardLink-Ablauf, der wiederum auf der entsprechenden 
Spezifikation [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) der gematik GmbH aufbaut.

Die Spezifikationen in diesem Dokument werden durch die folgende [asyncapi](https://www.asyncapi.com/)-Spezifikationen ergänzt:

* [eHealth-CardLink Prescription Communication Interface](https://github.com/eHealthCardLink/Spezifikation/blob/main/prescription-communication.yaml) der eHealth-CardLink-Taskforce, die auch als [html](https://ehealthcardlink.github.io/Spezifikation/prescription-communication/) verfügbar ist. 

## Versionierung

| Version | Datum | Beschreibung der wesentlichen Änderungen | 
| --- | --- |  --- |
| `1.0.0 (RC)` | 25.06.2024 | Initiale Version | 

## A.1 Ablauf beim Einlösen von E-Rezepten

Der grundlegende Ablauf für das Einlösen von E-Rezepten ist im nachfolgenden Sequenzdiagramm dargestellt. 

![Ablauf zum Einlösen von E-Rezepten in einer Apotheke](https://github.com/eHealthCardLink/Spezifikation/blob/main/img/E-Rezept-Einl%C3%B6sen-Ablauf-mit-ApoShop.svg)

### A.1.0 - CardLink-Basisablauf

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
Für die Kommunikation zwischen AVS und App müssen unter Berücksichtigung der entsprechenden FHIR-Profile 

* [KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview)
* [KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient)
* [KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview)
* [KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/~overview)
   
der Ressource [Medication](https://build.fhir.org/medication.html) und der zugehörigen "Technischen Anlage zur elektronischen Arzneimittelverordnung (E16A)" [[KBV_ITA_VGEX_TECHNISCHE_ANLAGE_ERP]](https://update.kbv.de/ita-update/DigitaleMuster/ERP/KBV_ITA_VGEX_Technische_Anlage_ERP.pdf) der Kassenärztlichen Bundesvereinigung folgende Datenstrukturen und Nachrichten definiert werden:

* MedicationSummary
* AvailablePrescriptionList 
* SelectedPrescriptionList

### A.2.1 MedicationSummary
Die MedicationSummary-Datenstruktur enthält die Rezeptierdaten  einer elektronischen Verordnung in den verschiedenen oben genannten Ausprägungen. 

Choice!

#### A.2.1.1 [KBV_PR_ERP_Medication_PZN](https://simplifier.net/eRezept/KBVPRERPMedicationPZN/~overview)

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

#### A.2.1.2 [KBV_PR_ERP_Medication_Ingredient](https://simplifier.net/erezept/kbvprerpmedicationingredient)

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

#### A.2.1.3 [KBV_PR_ERP_Medication_Compounding](https://simplifier.net/eRezept/KBVPRERPMedicationCompounding/~overview)

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

#### A.2.1.4 [KBV_PR_ERP_Medication_FreeText](https://simplifier.net/eRezept/KBVPRERPMedicationFreeText/)

In diesem Profil werden die Rezeptierdaten einer **freitextlichen Verordnung** abgebildet. 

| Element | Datentyp | Beschreibung | 
| --- | --- |  --- |
| `Kategorie` | string | siehe oben | 
| `Impfstoff` | boolean | siehe oben |
| `Freitextverordnung` | string | Dieses Feld enthält den Text einer Freitextverordnung. | 
| `Darreichungsform` | string | siehe oben |

### A.2.2 availablePrescriptionList 

### A.2.3 selectedPrescriptionList

