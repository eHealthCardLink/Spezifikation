<img align="right" width="80" height="80" src="https://github.com/eHealthCardLink/Spezifikation/blob/main/img/IOP-Logo.png"/><br/>

# eHealth-CardLink - Generische Basisspezifikation

<details>
  <summary><b>Inhaltsverzeichnis</b></summary>  
  Über dieses Dokument</br>
  Versionierung
  <ol>
    <li><b>Einleitung</b></li>
      <ul>1.1 Zielsetzung</ul>
      <ul>1.2 Methodik</ul>
    <li><b>Generische Basisspezifikation</b></li>
      <ul>2.1 Überblick</ul> 
      <ul>2.2 Grundlegende Abläufe beim eHealth-CardLink-Verfahren</ul>  
        <ul>2.2.1 Phase 0 - Vorbereitende Schritte und SMS-TAN-Verfahren</ul>
        <ul>2.2.2 Phase 1 - eGK mit App kontaktieren und Daten auslesen</ul>
        <ul>2.2.3 Phase 2 - Übermittlung der für den Prüfablauf relevanten Daten</ul>
        <ul>2.2.4 Phase 3 - Das Primärsystem ruft ReadVSD am Konnektor auf</ul>
        <ul>2.2.5 Phase 4 - Das Fachmodul VSDM startet die Onlineprüfung der eGK</ul>
        <ul>2.2.6 Phase 5 - Das Fachmodul VSDM im Konnektor führt Onlineprüfung der eGK durch</ul>
        <ul>2.2.7 Phase 6 - Das Fachmodul VSDM im Konnektor erstellt den Prüfungsnachweis</ul>
        <ul>2.2.8 Phase 7 - Der Konnektor liefert den Prüfungsnachweis in ReadVSDResponse zurück</ul>
      <ul>2.3 Nachrichten jenseits der gematik-Spezifikation</ul>
    <li><b>Anwendungsfallspezifische Ergänzungsmodule</b></li>
      <ul>3.1 Generelle Anforderungen an das application_interface</ul> 
      <ul>3.2 Existierende und geplante anwendungsspezifische Ergänzungsmodule</ul>      
  </ol>
</details>

## Über dieses Dokument

Dieses Dokument wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und ist die
**Basisspezifikation** für den eHealth-CardLink-Ablauf, der auf [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) aufbaut und diese Spezifikation ergänzt.

Die Spezifikationen in diesem Dokument werden durch die folgenden [asyncapi](https://www.asyncapi.com/)-Spezifikationen ergänzt:

* [eHealth-CardLink Card Communication Interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) der gematik GmbH 
* [eHealth-CardLink Card Communication Interface](https://github.com/eHealthCardLink/Spezifikation/blob/main/card-communication.yaml) der eHealth-CardLink-Taskforce ([html](https://ehealthcardlink.github.io/Spezifikation/card-communication/))

> [!NOTE]  
> Die gematik GmbH hat am **17.06.2024** zu verstehen gegeben, dass zeitnah eine Aktualisierung des API-Schemas erfolgen soll,
> welche die derzeit nicht mögliche Schemavalidierung und die nicht zur Spezifikation passenden Beispielnachrichten korrigieren wird.
> 
> Zitat: "Nach interner Prüfung können wir bestätigen, dass die asyncapi in zweierlei Hinsicht uneindeutig ist:<BR>
> •	Beispiele weichen teilweise von den Schemata ab, und <BR>
> •	allOf kann streng genommen nicht validiert werden, da ein Typ nicht gleichzeitig Object UND String sein kann.
> Wir planen, diese beiden Punkte zeitnah in der asyncapi anzupassen. Weitere Änderungen sind nicht geplant."

## Versionierung

| Version | Datum | Beschreibung der wesentlichen Änderungen | 
| --- | --- |  --- |
| `1.0.0 (RC)` | 25.06.2024 | Initiale Version | 

# 1. Einleitung

## 1.1 Zielsetzung
Die vorliegende Spezifikation wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und 
hat das Ziel, notwendige Ergänzungen der eHealth-CardLink-Spezifikation der gematik zu entwickeln, um ausgewählte Anwendungsfälle interoperabel und sicher durchführen zu können.
Zu diesem Zweck wird zunächst eine generische Basisspezifikation entwickelt, die durch anwendungsfallspezifische Module ergänzt wird.
## 1.2 Methodik
Anwendungsfälle und Anforderungen als Ausdruck normativer Festlegungen werden
durch eine eindeutige ID (`TF_xy`), Anforderungen zusätzlich durch die dem [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) entsprechenden, in
Großbuchstaben geschriebenen deutschen Schlüsselworte MUSS, DARF NICHT, SOLL,
SOLL NICHT, KANN gekennzeichnet.

# 2. Generische Basisspezifikation

## 2.1 Überblick
Die vorliegende Spezifikation ergänzt die eHealth-CardLink-Spezifkation [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) der gematik dahingehend, dass zusätzlich zum dort spezifizierten "Hinweg" von der App zum Primärsystem (PS) eines Leistungserbringers (z.B. Apothekenverwaltungssystem (AVS) einer Apotheke oder Praxisverwaltungssystem (PVS) eines Arztes oder Zahnarztes) hier der "Rückweg" vom PS zur App - das so genannte "**application_interface**" spezifiziert wird. 

![eHealth-CardLink-detailbild](https://github.com/eHealthCardLink/Spezifikation/assets/166988235/fb2bb66b-5605-4cc9-b9b8-d2f1e6127d00)

Darüber hinaus haben Detailbetrachtungen gezeigt, dass in der von der gematik bereitgestellten Schnittstellenspezifikation für das [card_interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) essentielle Nachrichten (z.B. für Behandlung der SMS-TAN) leider bislang komplett fehlen.
Diese notwendigen Ergänzungen der gematik-Spezifikation finden sich in [Abschnitt 2.3](#23-nachrichten-jenseits-der-gematik-spezifikation).

## 2.2 Grundlegende Abläufe beim eHealth-CardLink-Verfahren 

Der grundlegende Ablauf beim eHealth-CardLink-Verfahren gemäß [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) ist im nachfolgenden Sequenzdiagramm dargestellt, wobei die rot markierten Nachrichten über das derzeit von der gematik GmbH spezifizierte [card_interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) hinausgehen und in [Abschnitt 2.3](#23-nachrichten-jenseits-der-gematik-spezifikation) näher erläutert sind.

![Grundlegender eHealth-CardLink-Ablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/img/eHealth-CardLink-Ablauf.svg)

Die Nummerierung der Phasen  orientiert sich an den sieben in [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) (Abschnitt 2.1.1) grob beschriebenen Schritten:

#### 2.2.1 Phase 0 - Vorbereitende Schritte und SMS-TAN-Verfahren

* `(0.1)` - Damit der eHealth-CardLink-Dienst später mit dem Konnektor kommunizieren kann, muss zunächst ein Pairing stattfinden. Detials hierzu finden sich in [gemSpec_Kon](https://fachportal.gematik.de/fachportal-import/files/gemSpec_Kon_V5.21.0.pdf) (Abschnitt 4.1.4.3.3).
* `(0.2)` - Damit das Primärsystem später über eine verfügbare eGK informiert werden kann, muss es sich mittels **Subscribe** am Event-Service des Konnektors registrieren. Details hierzu finden sich in [gemSpec_Kon](https://fachportal.gematik.de/fachportal-import/files/gemSpec_Kon_V5.21.0.pdf) (Abschnitt 4.1.6.5.4).
* `(0.3)` - Der Konnektor schickt **SubscribeResponse** an das Primärsystem zurück.
* `(0.4)` - Bevor das eHealth-CardLink-Verfahren genutzt werden kann, muss die App installiert und ggf. konfiguriert werden. 
* `(0.5)` - Der Nutzer bzw. die Nutzerin kann den Zugriff auf den Telefonstatus erlauben. Sofern er den Zugriff ablehnt, muss er später seine Mobilfunknummer von Hand erfassen.
* `(0.6)` - Die App kann entweder systemseitig die Mobilfunknummer (Mobile Subscriber Number, MSN) aus dem Smartphone auslesen oder der Nutzer bzw. die Nutzerin muss die MSN von Hand erfassen.
* `(0.7)` - Sofern das nicht bereits durch die Auslieferung und Installation der App erfolgt ist, muss hier die entsprechende Leistungserbringerinstitution (LEI) konfiguriert werden.
* `(0.8)` - Nun etabliert die App eine Websocket-Verbindung mit dem eHealth-CardLink-Dienst.
* `(0.9)` - Der eHealth-CardLink-Dienst antwortet mit einer **sessionStatus**-Nachricht, die insbesondere die webSocketId enthält, welche die Websocket-Verbindung identifiziert.
* `(0.10)` - Nun wird mit der Nachricht **requestSMSCode** der Versand einer SMS mit einer Transaktionsnummer (TAN) angefordert
* `(0.11)` - In diesem Schritt wird die TAN im eHealth-CardLink-Dienst erzeugt und gespeichert.
* `(0.12)` - Die App erhält eine Nachricht **requestSMSResponse**, so dass sie den Nutzer bzw. die Nutzerin geeignet darauf hinweisen kann, dass die per SMS erhaltene TAN eingegeben werden soll.  
* `(0.13)` - In diesem Schritt wird die TAN per SMS an das Smartphone des Nutzers bzw. der Nutzerin geschickt.
* `(0.14)` - Der Nutzer bzw. die Nutzerin erfasst die erhaltene TAN in der App.
* `(0.15)` - Die TAN wird mit der Nachricht **confirmSMSCode** an den eHealth-CardLink-Dienst geschickt.
* `(0.16)` - Der eHealth-CardLink-Dienst überprüft, ob die im Schritt (0.b.11) erhaltene TAN mit der im Schritt (0.b.7) erzeugten und verschickten TAN übereinstimmt.
* `(0.17)` - Das Ergebnis der TAN-Validierung wird mit der Nachricht **confirmSMSCodeResponse** an die App zurückgemeldet. 

#### 2.2.2 Phase 1 - eGK mit App kontaktieren und Daten auslesen

Der Nutzer bzw. die Nutzerin hält die eGK an das NFC-fähige Smartphone ("Client des Nutzers").
Damit "übergibt" sie gewissermaßen die eGK an die an den eHealth-CardLink-Dienst angeschlossene LEI und autorisiert dadurch den nachfolgenden Ablauf zur Erstellung des VSDM-Prüfungsnachweises.

* `(1.1)` - In diesem Schritt stellt der Nutzer bzw. die Nutzerin die auf der eGK aufgedruckte "Card Access Number" (CAN) und die eGK bereit.
* `(1.2)` -  Die nutzt die CAN, um das PACE-Protokoll durchzuführen, um eine sichere Verbindung zwischen der eGK und der App zu etablieren.
* `(1.3)` - In diesem Schritt liest die App die für den weiteren Ablauf notwendigen Daten von der eGK aus.

#### 2.2.3 Phase 2 - Übermittlung der für den Prüfablauf relevanten Daten

Die für den Prüfablauf relevanten Daten der eGK werden von der App an den eHealth-
CardLink-Dienst übermittelt.

* `(2.1)` - Die App überträgt die aus der eGK ausgelesenen Daten in der **registerEGK**-Nachricht an den eHealth-CardLink-Dienst.
* `(2.2)` - In diesem Schritt wird die Verknüpfung der ICCSN der eGK mit der MSN geprüft.
* `(2.3)` - Sofern die eGK vorher bereits mit einer anderen MSN verknüpft war, wird die App in diesem Schritt mit der **ICCSNReassignment** Nachricht informiert.
* `(2.4)` - Sofern die eGK vorher mit einer anderen MSN verknüpft war, wird der Nutzer dieser vorherigen MSN per SMS informiert, um einen potenziellen Missbrauch erkennbar zu machen.
* `(2.5)` - In diesem Schritt werden die Vorgänge protokolliert.
* `(2.6)` - Der eHealth-CardLink-Dienst signalisiert dem Konnektor über das SICCT-Protokoll,
  dass eine eGK verfübar ist.  
* `(2.7)` - Der Konnektor signalisiert dem Primärsystem über den **Event**-Mechanismus, dass eine Karte verfügbar gemacht wurde. Detials hierzu finden sich in [gemSpec_Kon](https://fachportal.gematik.de/fachportal-import/files/gemSpec_Kon_V5.21.0.pdf) (Abschnitt 4.1.6.1). 
* `(2.8)` - Das Primärsystem schickt das **GetCards** Kommando an den Konnektor, um die zur aktuellen Websocket-Session gehörende(n) eGK(s) zu ermitteln.
* `(2.9)` - Die entsprechenden SICCT-Nachrichten, die zum **GetCards**-Aufruf korrespondieren, wreden an den eHealth-CardLink-Dienst geschickt.
* `(2.10)` - Die entsprechenden SICCT-Nachrichten, die zum **GetCards**-Aufruf korrespondieren, wreden an den eHealth-CardLink-Dienst geschickt.
* `(2.11)` - Die **GetCardsResponse**-Nachricht wird samt der zugehörigen ICCSN(s) an das Primärsystem zurückgeliefert.

#### 2.2.4 Phase 3 - Das Primärsystem ruft ReadVSD am Konnektor auf

Das Primärsystem (PS) ruft die Operation **ReadVSD** (siehe  [gemSpec_SST_PS_VSDM](https://fachportal.gematik.de/fachportal-import/files/gemSpec_SST_PS_VSDM_V1.5.0.pdf), Abschnitt 3.2) am Konnektor auf, wodurch zunächst eine einseitige Authentisierung der eGK durchgeführt wird.

* `(3.1)` - Das PS ruft **ReadVSD** am Konnektor auf.
* `(3.2)` - Der Konnektor tauscht mit dem eHealth-CardLink-Dienst die entsprechenden APDUs über SICCT aus, um die notwendigen eGK-Daten zu ermitteln.

> [!WARNING]  
> Es sei hier darauf hingewiesen, dass die Konnektor-internen Abläufe für das **ReadVSD**-Kommando leider **nicht** im Details spezifiziert und normiert sind, so dass die genauen Nachrichten bei den verschiedenen Konnektoren deshalb an dieser Stelle in Details voneinander abweichen können.

* `(3.3)` - Der Konnektor schickt über SICCT eine Challenge, die später für die einseitige Authentisierung der eGK genutzt wird.
* `(3.4)` - Der eHealth-CardLink-Dienst schickt die Challenge in der **sendAPDU**-Nachricht an die App.
* `(3.5)` - Die App schickt über den etablierten PACE-Kanal die Challenge im INTERNAL_AUTHENTICATE-Chipkartenkommand an die eGK.
* `(3.6)` - Die eGK erzeugt eine Signatur für die Challenge
* `(3.7)` - Die eGK schickt im Erfolgsfall die Signatur mit dem Statuscode "90 00" zurück zur App.
* `(3.8)` - Die App schickt die **sendAPDUResponse**-Nachricht zurück an den eHealth-CardLink-Dienst.
* `(3.9)` - Der eHealth-CardLink-Dienst schickt über SICCT die entsprechende Antwort an den Konnektor.
* `(3.10)` - Der Konnektor validiert die Signatur, wodurch die Echtheit der eGK nachgewiesen ist.

#### 2.2.5 Phase 4 - Das Fachmodul VSDM startet die Onlineprüfung der eGK

Das Fachmodul VSDM startet die Onlineprüfung und Gültigkeitsprüfung der eGK.

* `(4)` - Das VSDM-Fachmodul im Konnektor startet die Online-Gültigkeitsprüfung der eGK in Verbindung mit dem VSDM-Fachdienst.

#### 2.2.6 Phase 5 - Das Fachmodul VSDM im Konnektor führt Onlineprüfung der eGK durch

Der Fachdienst VSDM (UFS oder VSDD) verwendet als fachliche Information für die
Prüfziffer u.a. die KVNR und den aktuellen Zeitpunkt als Zeitstempel. Es wird mit dem
betreiberspezifischen Geheimnis ein Hashwert über die fachlichen Informationen
gebildet. Die fachlichen Informationen bilden zusammen mit dem Hashwert die
Prüfziffer. 

* `(5)` - Das VSDM-Fachmodul im Konnektor führt die restliche Online-Gültigkeitsprüfung der eGK in Verbindung mit dem VSDM-Fachdienst durch und liefert die Prüfziffer an den Konnektor zurück.

#### 2.2.7 Phase 6 - Das Fachmodul VSDM im Konnektor erstellt den Prüfungsnachweis

* `(6)` - Das Fachmodul VSDM im Konnektor erstellt den Prüfungsnachweis und fügt die vom Fachdienst
VSDM erhaltene Prüfziffer ein.

#### 2.2.8 Phase 7 - Der Konnektor liefert den Prüfungsnachweis in ReadVSDResponse zurück

Das Fachmodul VSDM liefert im Response die Versichertenstammdaten und den
Prüfungsnachweis an das PS. 

* `(7.1)` - Der Konnektor liefert in der **ReadVSDResponse**-Nachricht den Prüfungsnachweis zurück an das Primärsystem.
* `(7.2)` - Um den eHealth-CardLink-Dienst und später die App auf möglicherweise folgende anwendungsspezifische Nachrichten vorzubereiten, etabliert das Primärsystem eine Websocket-Verbindung, die zur entsprechenden ICCSN bzw. eGK korrespondiert.
* `(7.3)` - Der eHealth-CardLink-Dienst liefert die entsprechende webSocketId in der **sessionStatus**-Nachricht an das Primärsystem zurück.
* `(7.4)` - Der eHealth-CardLink-Dienst schickt die Nachricht **RegisterEGKFinish** an die App, um zu signalisieren, dass im Erfolgsfall nun anwendungsspezifische Abläufe, wie in [Abschnitt 3.2](#32-existierende-und-geplante-anwendungsspezifische-erg%C3%A4nzungsmodule) aufgelistet folgen können.

## 2.3 Nachrichten jenseits der gematik-Spezifikation

In der vorliegenden Spezifikation sind verschiedene (im obigen Sequenzdiagramm rot markierte) Nachrichten enthalten, die über die derzeitige 
[gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf)
Spezifikation der gematik hinausgehen:

* **sessionStatus** - liefert insbesondere die für die weitere Kommunikation notwendige webSocketId zurück.
* **requestSMSCode** - übermittelt die Mobilfunknummer und fordert eine entsprechende TAN per SMS an.
* **requestSMSCodeResponse** - meldet den Erhalt der Aufforderung zum Versand der TAN an die App zurück.
* **confirmSMSCode** - übermittelt die per SMS erhaltene TAN zur Prüfung an den eHealth-CardLink-Dienst.
* **ICCSNReassignment** - wird an die App gesendet, sofern die eGK vorher mit einer anderen Mobilfunknummr verknüpft war.
* **RegisterEGKFinish** - signalisiert der App, dass der Basisablauf abgeschlossen ist und nun  anwendungsfallspezifische Abläufe folgen können.

Die technischen Details und entsprechende Beispiele sind Gegenstand der [YAML](https://ehealthcardlink.github.io/Spezifikation/card-communication/)-Spezifikation.

# 3. Anwendungsfallspezifische Ergänzungsmodule

## 3.1 Generelle Anforderungen an das application_interface
Für das application_interface existieren folgende Anforderungen:
* `TF_01` Der Datenaustausch im application_interface MUSS auf Basis von Websockets gemäß [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) unter Verwendung von JSON-Nachrichten gemäß [RFC8259](https://datatracker.ietf.org/doc/html/rfc8259) erfolgen.
* `TF_02` Für den Datenaustausch im application_interface zwischen App und eHealth-CardLink MUSS der bereits etablierte TLS-Kanal genutzt werden, über den auch der Datenaustausch beim card_interface erfolgt.
* `TF_03` Für den Datenaustausch im application_interface zwischen AVS und eHealth-CardLink MUSS ein separater TLS-Kanal aufgebaut werden. 

## 3.2 Existierende und geplante anwendungsspezifische Ergänzungsmodule

| Anhang | Version | Datum | Beschreibung des anwendungsspezifischen Ergänzungsmoduls | 
| --- | --- |  --- | --- |
| [`Anhang A`](https://github.com/eHealthCardLink/Spezifikation/blob/main/Anhang-A-E-Rezept-Einl%C3%B6sen.md) | 1.0.0 (RC) | 25.06.2024 | Einlösen von E-Rezepten in einer Apotheke | 
| geplant |  |  | Anforderung eines Folgerezeptes | 
| geplant |  |  | Entfernter Versicherungsnachweis | 
| geplant |  |  | Ambulante Pflege | 
| geplant |  |  | Stationäre Pflege | 
| geplant |  |  | Videosprechstunde | 
| geplant |  |  | Mobile Szenarien für Leistungserbringer (Notarzt, Rettungssanitäter etc.) | 






