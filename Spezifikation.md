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
Die vorliegende Basisspezifikation ergänzt die eHealth-CardLink-Spezifkation [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) der gematik dahingehend, dass zusätzlich zum dort spezifizierten "Hinweg" von der App zum Primärsystem (PS) eines Leistungserbringers (z.B. Apothekenverwaltungssystem (AVS) einer Apotheke oder Praxisverwaltungssystem (PVS) eines Arztes oder Zahnarztes) hier der "Rückweg" vom PS zur App - das so genannte "**application_interface**" spezifiziert wird. 
![eHealth-CardLink-detailbild](https://github.com/eHealthCardLink/Spezifikation/assets/166988235/fb2bb66b-5605-4cc9-b9b8-d2f1e6127d00)

Darüber hinaus haben Detailbetrachtungen gezeigt, dass in der von der gematik bereitgestellten Schnittstellenspezifikation für das [card_interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) essentielle Nachrichten (z.B. für Behandlung der SMS-TAN) leider komplett fehlen.

## 2.2 Grundlegende Abläufe beim eHealth-CardLink-Verfahren gemäß [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf)

Der grundlegende Ablauf ist im nachfolgenden Sequenzdiagramm dargestellt, wobei die rot markierten Nachrichten über das derzeit spezifizierte 
[card_interface](https://github.com/gematik/api-ehcl/blob/main/ehcl/asyncapi.yaml) hinausgehen.

![eHealth-CardLink-Ablauf](https://github.com/eHealthCardLink/Spezifikation/blob/main/eHealth-CardLink-Ablauf.svg)

Die Nummerierung der Schritte orientiert sich an den sieben in [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) (Abschnitt 2.1.1) grob beschriebenen Schritten:

* *0.a* Damit der eHealth-CardLink-Dienst später mit dem Konnektor kommunizieren kann, muss zunächst ein Pairing stattfinden.
* *0.b* In einem ersten Schritt muss die App installiert und ggf. konfiguriert werdne.
* 
 
# 3. Anwendungsfallspezifische Ergänzungsmodule

## 3.1 Anforderungen an das application_interface
Für das application_interface existieren folgende Anforderungen:
* `TF_01` Für den Datenaustausch im application_interface zwischen App und eHealth-CardLink MUSS der bereits etablierte TLS-Kanal genutzt werden, über den auch der Datenaustausch beim card_interface erfolgt.
* `TF_02` Der Datenaustausch im application_interface MUSS auf Basis von Websockets gemäß [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) sowie einer Kombination aus JSON gemäß [RFC8259](https://datatracker.ietf.org/doc/html/rfc8259) und [REST](https://de.wikipedia.org/wiki/Representational_State_Transfer) erfolgen.

## 3.2 Einlösen eines E-Rezeptes in einer Apotheke

## 3.3 Anforderung eines Folgerezeptes
## 3.4 Entfernter Versicherungsnachweis
## 3.5 Ambulante Pflege
## 3.6 Mobile Szenarien für Leistungserbringer (Notarzt, Rettungssanitäter etc.)
## 3.7 Videosprechstunde





