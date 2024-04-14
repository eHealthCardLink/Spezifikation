# Einleitung
## Zielsetzung
Die vorliegende Spezifikation wurde in der [eHealth-CardLink-Taskforce](https://github.com/eHealthCardLink) entwickelt und 
hat das Ziel, notwendige Ergänzungen der eHealth-CardLink-Spezifikation der gematik zu entwickeln, um ausgewählte Anwendungsfälle interoperabel und sicher durchführen zu können.
Zu diesem Zweck wird zunächst eine generische Basisspezifikation entwickelt, die durch anwendungsfallspezifische Module ergänzt wird.
## Methodik
Anwendungsfälle und Anforderungen als Ausdruck normativer Festlegungen werden
durch eine eindeutige ID (`TF_xy`), Anforderungen zusätzlich durch die dem [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) entsprechenden, in
Großbuchstaben geschriebenen deutschen Schlüsselworte MUSS, DARF NICHT, SOLL,
SOLL NICHT, KANN gekennzeichnet.
# Generische Basisspezifikation
## Überblick
Die vorliegende Basisspezifikation ergänzt die eHealth-CardLink-Spezifkation [gemSpec_eHealth-CardLink](https://gemspec.gematik.de/downloads/gemSpec/gemSpec_eHealth-CardLink/gemSpec_eHealth-CardLink_V1.0.0.pdf) der gematik dahingehend, dass zusätzlich zum dort spezifizierten "Hinweg" von der App zum Primärsystem (PS) eines Leistungserbringers (z.B. Apothekenverwaltungssystem (AVS) einer Apotheke oder Praxisverwaltungssystem (PVS) eines Arztes oder Zahnarztes) hier der "Rückweg" vom PS zur App - das so genannte "**application_interface**" spezifiziert wird. 
![eHealth-CardLink-detailbild](https://github.com/eHealthCardLink/Spezifikation/assets/166988235/fb2bb66b-5605-4cc9-b9b8-d2f1e6127d00)
## Anforderungen an das application_interface
Für das application_interface existieren folgende Anforderungen:
* `TF_01` Für den Datenaustausch im application_interface zwischen App und eHealth-CardLink MUSS der bereits etablierte TLS-Kanal genutzt werden, über den auch der Datenaustausch beim card_interface erfolgt.
* `TF_02` Der Datenaustausch im application_interface MUSS auf Basis von Websockets gemäß [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) sowie einer Kombination aus JSON gemäß [RFC8259](https://datatracker.ietf.org/doc/html/rfc8259) und [REST](https://de.wikipedia.org/wiki/Representational_State_Transfer) erfolgen.
# Anwendungsfallspezifische Ergänzungsmodule
## Einlösen eines E-Rezeptes in einer Apotheke
## Anforderung eines Folgerezeptes
## Entfernter Versicherungsnachweis
## Ambulante Pflege
## Mobile Szenarien für Leistungserbringer (Notarzt, Rettungssanitäter etc.)
## Videosprechstunde

