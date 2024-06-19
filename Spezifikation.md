<img align="right" width="100" height="100" src="https://github.com/eHealthCardLink/Spezifikation/blob/main/img/IOP-Logo.png"/><br/>

# eHealth-CardLink - Generische Basisspezifikation

<details>
  <summary>Inhaltsverzeichnis</summary>
  <ol>
    <li>
      <a href="#ueber">Über dieses Dokument</a>
       <ul>
        <li><a href="#version">Versionierung</a></li>
      </ul>
       <ul>
        <li><a href="#intro">1. Einleitung</a></li>
      </ul>
    </li>
  </ol>
</details>

## [Über dieses Dokument](#ueber)

Dieses Dokument wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und ist die
**Basisspezifikation** für den eHealth-CardLink-Ablauf, der auf [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) aufbaut
und diese Spezifikation ergänzt.
Die Spezifikationen in diesem Dokument werden durch die folgenden [asyncapi](https://www.asyncapi.com/)-Spezifikationen ergänzt:
* [eHealth-CardLink Card Communication Interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) der gematik GmbH 
* [eHealth-CardLink Card Communication Interface](https://github.com/eHealthCardLink/api-ehcl-proposal/blob/additional-messages-reworked/ehcl/asyncapi.yaml) der eHealth-CardLink-Taskforce

> [!NOTE]  
> Die gematik GmbH hat am 17.06.2024 zu verstehen gegeben, dass in Kürze eine Aktualisierung ihrer derzeitn pezifikation erfolgen soll,
> welche die derzeit nicht mögliche Schemavalidierung und die nicht zur Spezifikation passenden Beispielnachrichten korrigieren wird. 

## Versionierung

| Version | Datum | Beschreibung der wesentlichen Änderungen | 
| --- | --- |  --- |
| `1.0.0 (RC)` | 19.06.2024 | Initiale Version | 

# 1. [Einleitung](#intro)

## 1.1 Zielsetzung
Die vorliegende Spezifikation wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und 
hat das Ziel, notwendige Ergänzungen der eHealth-CardLink-Spezifikation der gematik zu entwickeln, um ausgewählte Anwendungsfälle interoperabel und sicher durchführen zu können.
Zu diesem Zweck wird zunächst eine generische Basisspezifikation entwickelt, die durch anwendungsfallspezifische Module ergänzt wird.
## 1.2 Methodik
Anwendungsfälle und Anforderungen als Ausdruck normativer Festlegungen werden
durch eine eindeutige ID (`TF_xy`), Anforderungen zusätzlich durch die dem [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) entsprechenden, in
Großbuchstaben geschriebenen deutschen Schlüsselworte MUSS, DARF NICHT, SOLL,
SOLL NICHT, KANN gekennzeichnet.

# 2. [Generische Basisspezifikation](#basis)

## 2.1 Überblick
Die vorliegende Basisspezifikation ergänzt die eHealth-CardLink-Spezifkation [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) der gematik dahingehend, dass zusätzlich zum dort spezifizierten "Hinweg" von der App zum Primärsystem (PS) eines Leistungserbringers (z.B. Apothekenverwaltungssystem (AVS) einer Apotheke oder Praxisverwaltungssystem (PVS) eines Arztes oder Zahnarztes) hier der "Rückweg" vom PS zur App - das so genannte "**application_interface**" spezifiziert wird. 
![eHealth-CardLink-detailbild](https://github.com/eHealthCardLink/Spezifikation/assets/166988235/fb2bb66b-5605-4cc9-b9b8-d2f1e6127d00)

Darüber hinaus haben Detailbetrachtungen gezeigt, dass in der von der gematik bereitgestellten Schnittstellenspezifikation für das [card_interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) essentielle Nachrichten (z.B. für Behandlung der SMS-TAN) leider komplett fehlen.

## 2.2 Grundlegende Abläufe beim eHealth-CardLink-Verfahren gemäß [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf)

Der grundlegende Ablauf ist im nachfolgenden Sequenzdiagramm dargestellt, wobei die rot markierten Nachrichten über das derzeit spezifizierte 
[card_interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) hinausgehen.

![Grundlegender eHealth-CardLink-Ablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/eHealth-CardLink-Ablauf.svg)

Die Nummerierung der Schritte orientiert sich an den sieben in [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) (Abschnitt 2.1.1) grob beschriebenen Schritten:

* **(0.a)** - Damit der eHealth-CardLink-Dienst später mit dem Konnektor kommunizieren kann, muss zunächst ein Pairing stattfinden. Detials hierzu finden sich in [gemSpec_Kon](https://fachportal.gematik.de/fachportal-import/files/gemSpec_Kon_V5.21.0.pdf) (Abschnitt 4.1.4.3.3).
* **(0.b)** - In einem ersten Schritt muss die App installiert und ggf. konfiguriert werden. 
* **(0.b.1)** - Der Nutzer kann den Zugriff auf den Telefonstatus erlauben. Sofern er den Zugriff ablehnt, muss er später seine Mobilfunknummer von Hand erfassen.
* **(0.b.2)** - Die App kann entweder systemseitig die Mobilfunknummer (Mobile Subscriber Number, MSN) aus dem Smartphone auslesen oder der Nutzer muss die MSN von Hand erfassen.


 
# 3. [Anwendungsfallspezifische Ergänzungsmodule](#ergaenzungsmodule)

## 3.1 Generelle Anforderungen an das application_interface
Für das application_interface existieren folgende Anforderungen:
* `TF_01` Der Datenaustausch im application_interface MUSS auf Basis von Websockets gemäß [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) unter Verwendung von JSON-Nachrichten gemäß [RFC8259](https://datatracker.ietf.org/doc/html/rfc8259) erfolgen.
* `TF_02` Für den Datenaustausch im application_interface zwischen App und eHealth-CardLink MUSS der bereits etablierte TLS-Kanal genutzt werden, über den auch der Datenaustausch beim card_interface erfolgt.
* `TF_03` Für den Datenaustausch im application_interface zwischen AVS und eHealth-CardLink MUSS ein separater TLS-Kanal aufgebaut werden. 

## 3.2 Existierende und geplante anwendungsspezifische Ergänzungsmodule

| Anhang | Version | Datum | Beschreibung des anwendungsspezifischen Ergänzungsmoduls | 
| --- | --- |  --- | --- |
| [`Anhang A`](https://github.com/eHealthCardLink/Spezifikation/blob/main/Anhang-A-E-Rezept-Einl%C3%B6sen.md) | 1.0.0 (RC) | 19.06.2024 | Einlösen von E-Rezepten in einer Apotheke | 
| geplant |  |  | Anforderung eines Folgerezeptes | 
| geplant |  |  | Entfernter Versicherungsnachweis | 
| geplant |  |  | Ambulante Pflege | 
| geplant |  |  | Stationäre Pflege | 
| geplant |  |  | Videosprechstunde | 
| geplant |  |  | Mobile Szenarien für Leistungserbringer (Notarzt, Rettungssanitäter etc.) | 






