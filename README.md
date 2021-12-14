# KEX-Vorgang-Update-API

> ⚠️ You'll find German domain-specific terms in the documentation, for translations and further explanations please refer to our [glossary](https://docs.api.europace.de/common/glossary/)

## General

APIs for updating data in KreditSmart-Vorgängen.  
All APIs documented here are [GraphQL-APIs](https://docs.api.europace.de/privatkredit/graphql/).

    https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge

> ⚠️ This API is continuously developed. Therefore we expect
> all users to align with the "[Tolerant Reader Pattern](https://martinfowler.com/bliki/TolerantReader.html)", which requires clients to be
> tolerant towards compatible API-Changes when reading and processing the data. This means:
>
> 1. unknown properties must not result in errors
>
> 2. Strings with a restricted set of values (Enums) must support new unknown values
>
> 3. sensible usage of HTTP-Statuscodes, even if they are not explicitly documented
>

<!-- https://opensource.zalando.com/restful-api-guidelines/#108 -->

### Authentication

These APIs are secured by the OAuth client credentials flow using the [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/).
To use these APIs your OAuth2-Client needs the following Scopes:

| Scope                          | Label in Partnermanagement             | Description                  |
|--------------------------------|----------------------------------------|------------------------------|
| privatkredit:vorgang:schreiben | KreditSmart-Vorgänge anlegen/verändern | Scope for updating a Vorgang |

### GraphQL-Requests

These APIs accept data with the Content-Type "**application/json**" with UTF-8 encoding.
The fields inside a block can be sent in any order.

The APIs support all common GraphQL formats. More information can be found at [https://graphql.org/learn/queries/](https://graphql.org/learn/queries/).

The body of a GraphQL request contains the field `query`, which includes the GraphQL query as a String. Parameters can be set directly in the query or defined as variables. The variables can be sent in the `variables` field of the body as a key-value-map.
All our examples use variables.

    {
      "query": "...",
      "variables": { ... }
    }

### Error Codes

One of the special features in GraphQL is that most errors are not reflected via HTTP error codes.
In many cases you receive a status code 200, even though an error has occurred. These GraphQL errors can be found in the `errors` field of the response body.
More information about error codes can be found [here](https://docs.api.europace.de/privatkredit/graphql/#error-handling).

### HTTP-Status Errors

| Error Code | Message               | Description                     |
|------------|-----------------------|---------------------------------|
| 401        | Unauthorized          | Authentication failed           |
| 403        | Forbidden             | The API client misses a scope   |
| 415        | Unsupported MediaType | The wrong content type was used |

#### GraphQL Errors

| Error Code | Message     | Description                                                                                    |
|------------|-------------|------------------------------------------------------------------------------------------------|
| 400        | Bad Request | Request format is invalid (mandatory fields are missing, wrong parameter names or values, ...) |
| 403        | Forbidden   | The authenticated user does not have sufficient rights                                         |
| 404        | Not Found   | The Vorgang does not exist                                                                     |
| 410        | Gone        | The Vorgang was deleted in the meantime                                                        |

## Antragstellerdaten anpassen

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Die `antragstellerId` muss in dem Vorgang vorhanden sein und auf einen bereits vorhandenen Antragsteller referenzieren.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Wenn Felder, die keinen Default Wert besitzen, nicht angegeben werden, werden die vorigen Werte entfernt.

### Personendaten anpassen

**updatePersonendaten** ( vorgangsnummer: String!, antragstellerId: String!, personendaten: [Personendaten](#personendaten)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Personendaten](#personendaten) für einen Antragsteller eines Vorgangs anpassen.

#### Beispiel

##### POST Request

    POST https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge
    Authorization: Bearer xxxx
    Content-Type: application/json

    {
      "query": "mutation personendaten($vorgangsnummer: String!, $antragstellerId: String!) {  
        updatePersonendaten(vorgangsnummer: $vorgangsnummer, antragstellerId: $antragstellerId, personendaten: { 
          vorname: "Max"
          nachname: "Mustermann"
        }) { 
          messages
        } 
      }",
      "variables": {
        "vorgangsnummer": "ABC123",
        "antragstellerId": "12345678-abcd-wxyz-0987-1234567890ab"
      }
    }

##### POST Response

    {
      "data": {
        "messages": []
      },
      "errors": []
    }

### Wohnsituation anpassen

**updateWohnsituation** ( vorgangsnummer: String!, antragstellerId: String!, wohnsituation: [Wohnsituation](#wohnsituation)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Wohnsituation](#wohnsituation) für einen Antragsteller eines Vorgangs anpassen.

#### Beispiel

##### POST Request

    POST https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge
    Authorization: Bearer xxxx
    Content-Type: application/json

    {
      "query": "mutation wohnsituation($vorgangsnummer: String!, $antragstellerId: String!) {  
        updateWohnsituation(vorgangsnummer: $vorgangsnummer, antragstellerId: $antragstellerId, wohnsituation: { 
          wohnart: ZUR_MIETE
          anzahlPersonenImHaushalt: 1
        }) { 
          messages
        } 
      }",
      "variables": {
        "vorgangsnummer": "ABC123",
        "antragstellerId": "12345678-abcd-wxyz-0987-1234567890ab"
      }
    }

### Herkunft anpassen

**updateHerkunft** ( vorgangsnummer: String!, antragstellerId: String!, herkunft: [Herkunft](#herkunft)!  ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Herkunft](#herkunft) für einen Antragsteller eines Vorgangs anpassen.

#### Beispiel

##### POST Request

    POST https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge
    Authorization: Bearer xxxx
    Content-Type: application/json

    {
      "query": "mutation herkunft($vorgangsnummer: String!, $antragstellerId: String!) {  
        updateHerkunft(vorgangsnummer: $vorgangsnummer, antragstellerId: $antragstellerId, herkunft: { 
          staatsangehoerigkeit: DE
          steuerId: "11345678904"
        }) { 
          messages
        } 
      }",
      "variables": {
        "vorgangsnummer": "ABC123",
        "antragstellerId": "12345678-abcd-wxyz-0987-1234567890ab"
      }
    }

##### POST Response

    {
      "data": {
        "messages": []
      },
      "errors": []
    }

### Beschäftigung anpassen

**updateBeschaeftigung** ( vorgangsnummer: String!, antragstellerId: String!, beschaeftigung: [Beschaeftigung](#beschaeftigung)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Beschaeftigung](#beschaeftigung) zu einem Antragsteller eines Vorgangs anpassen.

#### Hinweise

* Die [Beschaeftigung](#beschaeftigung) berücksichtigt genau eine Beschäftigungsart und nutzt dann das dazu korrespondierende Feld für die Aktualisierung.

#### Beispiel

##### POST Request

    POST https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge
    Authorization: Bearer xxxx
    Content-Type: application/json

    {
      "query": "mutation beschaeftigung($vorgangsnummer: String!, $antragstellerId: String!) {  
        updateBeschaeftigung(vorgangsnummer: $vorgangsnummer, antragstellerId: $antragstellerId, beschaeftigung: { 
          beschaeftigungsart:ARBEITER,
          arbeiter: {
            beschaeftigungsverhaeltnis: {
              nettoeinkommenMonatlich: 2345.67,
              befristung: UNBEFRISTET,
              beschaeftigtSeit: "2009-01-02",
              arbeitgeber: {
                name: "Firmenname",
                branche: INFORMATION_KOMMUNIKATION,
                anschrift: {
                  ort: "Berlin"
                }
              }
            }
          }
        }) { 
          messages
        } 
      }",
      "variables": {
        "vorgangsnummer": "ABC123",
        "antragstellerId": "12345678-abcd-wxyz-0987-1234567890ab"
      }
    }

##### POST Response

    {
      "data": {
        "messages": []
      },
      "errors": []
    }

## Haushaltspositionen anpassen

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Die in dem Feld `antragstellerIds` verwendeten IDs müssen in dem Vorgang vorhanden sein und auf bereits vorhandene Antragsteller referenzieren.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Wenn Felder, die keinen Default Wert besitzen, nicht angegeben werden, werden die vorigen Werte entfernt.
* Die im Feld `id` verwendete ID muss eine existierend Haushaltsposition vom entsprechenden Typ referenzieren.

### Einkunft aus Nebentaetigkeit anpassen

#### Hinweise

* Eine Einkunft aus einer Nebentätigkeit kann nur einem Antragsteller zugeordnet sein. Falls mehrere Antragsteller angegeben werden gibt es einen GraphQL-Fehler mit dem Fehlercode 422 - UNPROCESSABLE
  ENTITY.

**addEinkunftAusNebentaetigkeit** ( vorgangsnummer String!, einkunftAusNebentaetigkeit [EinkunftAusNebentaetigkeit](#einkunftausnebentaetigkeit)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Eine Einkunft aus einer Nebentätigkeit einem Vorgang hinzufügen. Die Response enthält die `id` der angelegten Haushaltsposition. Diese `id` kann als Referenz für weitere Änderungen benutzt werden.

**updateEinkunftAusNebentaetigkeit** ( vorgangsnummer: String!, id: String!, einkunftAusNebentaetigkeit [EinkunftAusNebentaetigkeit](#einkunftausnebentaetigkeit)! ) -> [BasicResponse](#basicresponse)!

> Eine existierende Einkunft aus einer Nebentätigkeit ändern. Die Haushaltsposition wird per `id` referenziert.

**deleteEinkunftAusNebentaetigkeit** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Eine existierende Einkunft aus einer Nebentätigkeit löschen. Die Haushaltsposition wird per `id` referenziert.

### Immobilie anpassen

**addImmobilie** ( vorgangsnummer: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Eine Immobilie einem Vorgang hinzufügen. Die Response enthält die `id` der angelegten Immobilie. Diese `id` kann als Referenz für weitere Änderungen benutzt werden.

**updateImmobilie** ( vorgangsnummer: String!, id: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicResponse](#basicresponse)!

> Eine existierende Immobilie ändern. Die Immobilie wird per `id` referenziert.

**deleteImmobilie** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Eine existierende Immobilie löschen. Die Immobilie wird per `id` referenziert.

### Mietausgabe anpassen

**addMietausgabe** ( vorgangsnummer String!, mietausgabe [Mietausgabe](#mietausgabe)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Eine Mietausgabe einem Vorgang hinzufügen. Die Response enthält die `id` der angelegten Mietausgabe. Diese `id` kann als Referenz für weitere Änderungen benutzt werden.

**updateMietausgabe** ( vorgangsnummer: String!, id: String!, mietausgabe [Mietausgabe](#mietausgabe)! ) -> [BasicResponse](#basicresponse)!

> Eine existierende Mietausgabe ändern. Die Mietausgabe wird per `id` referenziert.

**deleteMietausgabe** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Eine existierende Mietausgabe löschen. Die Mietausgabe wird per `id` referenziert.

### Private Krankenversicherung anpassen

#### Hinweise

* Eine private Krankenversicherung kann nur einem Antragsteller zugeordnet sein. Falls mehrere Antragsteller angegeben werden gibt es einen GraphQL-Fehler mit dem Fehlercode 422 - UNPROCESSABLE
  ENTITY.

**addPrivateKrankenversicherung** ( vorgangsnummer String!, privateKrankenversicherung [PrivateKrankenversicherung](#private-krankenversicherung)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Eine private Krankenversicherung einem Vorgang hinzufügen. Die Response enthält die `id` der angelegten Haushaltsposition. Diese `id` kann als Referenz für weitere Änderungen benutzt werden.

**updatePrivateKrankenversicherung** ( vorgangsnummer: String!, id: String!, privateKrankenversicherung [PrivateKrankenversicherung](#private-krankenversicherung)! )
-> [BasicResponse](#basicresponse)!

> Eine existierende private Krankenversicherung ändern. Die Haushaltsposition wird per `id` referenziert.

**deletePrivateKrankenversicherung** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Eine existierende private Krankenversicherung löschen. Die Haushaltsposition wird per `id` referenziert.

## Finanzierungswunsch anpassen

**updateFinanzierungswunsch** ( vorgangsnummer: String!, finanzierungswunsch: [Finanzierungswunsch](#finanzierungswunsch)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man den [Finanzierungswunsch](#finanzierungswunsch) eines Vorgangs anpassen.

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Das Feld `Finanzierungswunsch.rateMonatlich` wird nur berücksichtigt, wenn keine `laufzeitInMonaten` angegeben ist.
* Wenn das Feld `Finanzierungswunsch.ratenzahlungstermin` nicht angegeben wird, wird der Wert `MONATSENDE` verwendet.
* Wenn Felder, die keinen Default Wert besitzen, nicht angegeben werden, werden die vorigen Werte entfernt.

### Beispiel

#### POST Request

    POST https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge
    Authorization: Bearer xxxx
    Content-Type: application/json

    {
      "query": "mutation finanzierungswunsch($vorgangsnummer: String!) {  
        updateFinanzierungswunsch(vorgangsnummer: $vorgangsnummer, finanzierungswunsch: { kreditbetrag: 10000 }){
          messages
        }
      }",
      "variables": {
        "vorgangsnummer": "ABC123"
      }
    }

#### POST Response

    {
      "data": {
        "messages": []
      },
      "errors": []
    }

## Kommentare hinzufügen

**addKommentare** ( vorgangsnummer: String!, kommentare: [String!]! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man ein oder mehrere Kommentare zu einem Vorgang hinzufügen.

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates Übernahmerechte auf den Kundenbetreuer haben.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Leere Strings werden ignoriert und nicht als Kommentar importiert.

### Beispiel

#### POST Request

    POST https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge
    Authorization: Bearer xxxx
    Content-Type: application/json

    {
      "query": "mutation kommentare($vorgangsnummer: String!) {  
        addKommentare(vorgangsnummer: $vorgangsnummer, kommentare: ["kommentar 1", "kommentar 2"]){
          messages
        }
      }",
      "variables": {
        "vorgangsnummer": "ABC123"
      }
    }

#### POST Response

    {
      "data": {
        "messages": []
      },
      "errors": []
    }

## Request-Datentypen

### Finanzierungswunsch

    {
        "laufzeitInMonaten": Integer
        "ratenzahlungstermin": Ratenzahlungstermin
        "provisionswunschInProzent": BigDecimal
        "kreditbetrag": BigDecimal
        "rateMonatlich": BigDecimal
    }

#### Ratenzahlungstermin

        "MONATSENDE" | "MONATSMITTE"

### Beschaeftigung

    {
        "beschaeftigungsart": "ANGESTELLTER" | "ARBEITER" | "ARBEITSLOSER" | "BEAMTER" | "FREIBERUFLER" | "HAUSFRAU" | "RENTNER" | "SELBSTSTAENDIGER",
        "arbeiter": Arbeiter,
        "angestellter": Angestellter,                
        "arbeitsloser": Arbeitsloser,
        "beamter": Beamter,
        "selbststaendiger": Selbstständiger,
        "freiberufler": Freiberufler,
        "hausfrau": Hausfrau,
        "rentner": Rentner
    }

Die `Beschaeftigungsart` bestimmt die Beschäftigung und damit das dazu korrespondierende Feld. Beispielsweise wird für die `beschaeftigungsart=ARBEITER`
die Daten unter dem Knoten `arbeiter` genutzt, bei der `beschaeftigungsart=BEAMTER` entsprechend der Knoten `beamter`. Werden darüber hinaus weitere Felder befüllt so werden diese ignoriert.<BR/>
Ist keine `Beschaeftigungsart` gesetzt oder der zur angegebenen Beschäftigungsart passende Knoten nicht befüllt, werden alle Felder ignoriert.

#### Arbeiter

    {
        "beschaeftigungsverhaeltnis": {
            "berufsbezeichnung": String,
            "nettoeinkommenMonatlich": BigDecimal,
            "arbeitgeber": Firma,
            "beschaeftigtSeit": "YYYY-MM-DD",
            "befristung": "BEFRISTET" | "UNBEFRISTET",
            "befristetBis": "YYYY-MM-DD",
            "inProbezeit": true | false
        },
        "vorherigesBeschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma
            "beschaeftigtSeit": "YYYY-MM-DD",
            "beschaeftigtBis": "YYYY-MM-DD"
        }
    }

#### Angestellter

    {
        "beschaeftigungsverhaeltnis": {
            "berufsbezeichnung": String,
            "nettoeinkommenMonatlich": BigDecimal,
            "arbeitgeber": Firma,
            "beschaeftigtSeit": "YYYY-MM-DD",
            "befristung": "BEFRISTET" | "UNBEFRISTET",
            "befristetBis": "YYYY-MM-DD",
            "inProbezeit": true | false
        },
        "vorherigesBeschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma
            "beschaeftigtSeit": "YYYY-MM-DD",
            "beschaeftigtBis": "YYYY-MM-DD"
        }
    }

#### Arbeitsloser

    {
        "sonstigesEinkommenMonatlich": BigDecimal
    }

#### Beamter

    {
        "beschaeftigungsverhaeltnis": {
            "berufsbezeichnung": String,
            "inProbezeit": true | false,
            "nettoeinkommenMonatlich": BigDecimal,
            "verbeamtetSeit": "YYYY-MM-DD",
            "arbeitgeber": Firma,
            "beschaeftigtSeit": "YYYY-MM-DD"
        },
        "vorherigesBeschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma,
            "beschaeftigtSeit": "YYYY-MM-DD",
            "beschaeftigtBis": "YYYY-MM-DD"
        }
    }

#### Selbstständiger

    {
        "berufsbezeichnung": String,
        "selbststaendigSeit": "YYYY-MM-DD",
        "firma": Firma,
        "nettoeinkommenJaehrlich": BigDecimal,
        "bruttoEinkommenLaufendesJahr": BigDecimal,
        "einkommenssteuerLaufendesJahr": BigDecimal,
        "abschreibungenLaufendesJahr": BigDecimal,
        "bruttoEinkommenLetztesJahr": BigDecimal,
        "einkommenssteuerLetztesJahr": BigDecimal,
        "abschreibungenLetztesJahr": BigDecimal,
        "einkommenssteuerVor2Jahren": BigDecimal,
        "bruttoEinkommenVor2Jahren": BigDecimal,
        "abschreibungenVor2Jahren": BigDecimal,
        "bruttoEinkommenVor3Jahren": BigDecimal,
        "einkommenssteuerVor3Jahren": BigDecimal,
        "abschreibungenVor3Jahren": BigDecimal
    }

#### Freiberufler

    {
        "berufsbezeichnung": String,
        "selbststaendigSeit": "YYYY-MM-DD",
        "firma": Firma,
        "nettoeinkommenJaehrlich": BigDecimal,
        "bruttoEinkommenLaufendesJahr": BigDecimal,
        "einkommenssteuerLaufendesJahr": BigDecimal,
        "abschreibungenLaufendesJahr": BigDecimal,
        "bruttoEinkommenLetztesJahr": BigDecimal,
        "einkommenssteuerLetztesJahr": BigDecimal,
        "abschreibungenLetztesJahr": BigDecimal,
        "einkommenssteuerVor2Jahren": BigDecimal,
        "bruttoEinkommenVor2Jahren": BigDecimal,
        "abschreibungenVor2Jahren": BigDecimal,
        "bruttoEinkommenVor3Jahren": BigDecimal,
        "einkommenssteuerVor3Jahren": BigDecimal,
        "abschreibungenVor3Jahren": BigDecimal
    }

#### Hausfrau

    {
        "sonstigesEinkommenMonatlich": BigDecimal
    }

#### Rentner

    {
        "staatlicheRenteMonatlich": BigDecimal,
        "rentnerSeit": "YYYY-MM-DD",
        "rentenversicherung": {
            "name": String,
            "anschrift": Anschrift
        }
    }

#### Anschrift

    {
        "strasse": String,
        "hausnummer": String,
        "plz": String,
        "ort": String,
        "land": Country
    }

#### Country

Die Übermittlung erfolgt im Format [ISO-3166/ALPHA-2](https://de.wikipedia.org/wiki/ISO-3166-1-Kodierliste)

Zusätzlich gibt es den Wert "SONSTIGE"

    "AD" | "AE" | "AF" | "AG" | "AL" | "AM" | "AO" | "AR" | "AT" | "AU" | "AZ" | "BA" | "BB" | "BD" | "BE" | "BF" | "BG" | "BH" | "BI" | "BJ" | "BN" | "BO" | "BR" | "BS" | "BT" | "BW" | "BY" | "BZ" | "CA" | "CD" | "CF" | "CG" | "CH" | "CI" | "CK" | "CL" | "CM" | "CN" | "CO" | "CR" | "XK" | "CU" | "CV" | "CY" | "CZ" | "DE" | "DJ" | "DK" | "DM" | "DO" | "DZ" | "EC" | "EE" | "EG" | "ER" | "ES" | "ET" | "FI" | "FJ" | "FM" | "FR" | "GA" | "GB" | "GD" | "GE" | "GH" | "GM" | "GN" | "GQ" | "GR" | "GT" | "GW" | "GY" | "HN" | "HR" | "HT" | "HU" | "ID" | "IE" | "IL" | "IN" | "IQ" | "IR" | "IS" | "IT" | "JM" | "JO" | "JP" | "KE" | "KG" | "KH" | "KI" | "KM" | "KN" | "KP" | "KR" | "KW" | "KZ" | "LA" | "LB" | "LC" | "LI" | "LK" | "LR" | "LS" | "LT" | "LU" | "LV" | "LY" | "MA" | "MC" | "MD" | "ME" | "MG" | "MH" | "MK" | "ML" | "MM" | "MN" | "MR" | "MT" | "MU" | "MV" | "MW" | "MX" | "MY" | "MZ" | "NA" | "NE" | "NG" | "NI" | "NL" | "NO" | "NP" | "NR" | "NU" | "NZ" | "OM" | "PA" | "PE" | "PG" | "PH" | "PK" | "PL" | "PS" | "PT" | "PW" | "PY" | "QA" | "RO" | "RS" | "RU" | "RW" | "SA" | "SB" | "SC" | "SD" | "SE" | "SG" | "SI" | "SK" | "SL" | "SM" | "SN" | "SO" | "SR" | "SS" | "ST" | "SV" | "SY" | "SZ" | "TD" | "TG" | "TH" | "TJ" | "TL" | "TM" | "TN" | "TO" | "TR" | "TT" | "TV" | "TZ" | "UA" | "UG" | "US" | "UY" | "UZ" | "VA" | "VC" | "VE" | "VN" | "VU" | "WS" | "YE" | "ZA" | "ZM" | "ZW" | "SONSTIGE"

#### Firma

    {
        "name": String,
        "anschrift": Anschrift,
        "branche": "LANDWIRTSCHAFT_FORSTWIRTSCHAFT_FISCHEREI" | "ENERGIE_WASSERVERSORGUNG_BERGBAU" | "VERARBEITENDES_GEWERBE" | "BAUGEWERBE" | "HANDEL" | "VERKEHR_LOGISTIK" | "INFORMATION_KOMMUNIKATION" | "GEMEINNUETZIGE_ORGANISATION" | "KREDITINSTITUTE_VERSICHERUNGEN" | "PRIVATE_HAUSHALTE" | "DIENSTLEISTUNGEN" | "OEFFENTLICHER_DIENST" | "GEBIETSKOERPERSCHAFTEN" | "HOTEL_GASTRONOMIE" | "ERZIEHUNG_UNTERRICHT" | "KULTUR_SPORT_UNTERHALTUNG" | "GESUNDHEIT_SOZIALWESEN"
    }

### EinkunftAusNebentaetigkeit

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal,
        "beginnDerTaetigkeit": "YYYY-MM-DD"
    }

### Immobilie

    {
        "antragstellerIds": [ String ],
        "bezeichnung": String,
        "darlehen": [ Darlehen ],
        "immobilienart": "EIGENTUMSWOHNUNG" | "EINFAMILIENHAUS" | "MEHRFAMILIENHAUS" | "BUEROGEBAEUDE",
        "mieteinnahmenKaltMonatlich": BigDecimal,
        "mieteinnahmenWarmMonatlich": BigDecimal,
        "nebenkostenMonatlich": BigDecimal,
        "nutzungsart": "EIGENGENUTZT" | "VERMIETET" | "EIGENGENUTZT_UND_VERMIETET",
        "vermieteteWohnflaeche": Integer,
        "wert": BigDecimal,
        "wohnflaeche": Integer
    }

#### Darlehen

    {
        "rateMonatlich": BigDecimal,
        "restschuld": BigDecimal,
        "zinsbindungBis": "YYYY-MM-DD"
    }

### Herkunft

    {
        "arbeitserlaubnisVorhanden": true | false,
        "aufenthaltBefristetBis": "YYYY-MM-DD",
        "arbeitserlaubnisBefristetBis": "YYYY-MM-DD",
        "inDeutschlandSeit": "YYYY-MM-DD",
        "staatsangehoerigkeit": Country,
        "aufenthaltstitel": "VISUM" | "AUFENTHALTSERLAUBNIS" | "NIEDERLASSUNGSERLAUBNIS" | "ERLAUBNIS_ZUM_DAUERAUFENTHALT_EU",
        "steuerId": String
    }

### Mietausgabe

    {
        "antragstellerIds": [ String ],
        "BigDecimal": BigDecimal
    }

### Personendaten

    {
        "anrede": "FRAU" | "HERR",
        "email": String,
        "familienstand": "LEDIG" | "VERHEIRATET" | "GESCHIEDEN" | "VERWITWET" | "GETRENNT_LEBEND" | "EHEAEHNLICHE_LEBENSGEMEINSCHAFT" | "EINGETRAGENE_LEBENSPARTNERSCHAFT",
        "geburtsdatum": "YYYY-MM-DD",
        "geburtsland": Country,
        "geburtsname": String,
        "geburtsort": String,
        "nachname": String,
        "telefonGeschaeftlich": String,
        "telefonPrivat": String,
        "titel": [ "DOKTOR" | "PROFESSOR" ],
        "vorname": String
    }

### Private Krankenversicherung

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### Wohnsituation

    {
        "anschrift": Wohnanschrift,
        "gemeinsamerHaushalt": true | false,
        "wohnart": "ZUR_MIETE" | "ZUR_UNTERMIETE" | "IM_EIGENEN_HAUS" | "BEI_DEN_ELTERN",
        "anzahlPersonenImHaushalt": Integer,
        "anzahlPkw": Integer,
        "voranschrift": Wohnanschrift
    }

#### Wohnanschrift

    {
        "strasse": String,
        "hausnummer": String,
        "plz": String,
        "ort": String,
        "land": Country,
        "wohnhaftSeit": "YYYY-MM-DD"
    }

## Response-Datentypen

> Wenn serverseitig Daten angepasst werden mussten, um eine valide Verarbeitung zu gewährleisten, werden diese Anpassungen als Meldungen zurückgegeben, um den Client zu informieren. Diese Meldungen sind KEINE Fehlermeldungen.

### BasicResponse

    {
        "messages" [ String ]
    }

### BasicCreatedResponse

    {
        "messages": [ String ]
        "id": String
    }

## Nutzungsbedingungen

Die APIs werden unter folgenden [Nutzungsbedingungen](https://docs.api.europace.de/nutzungsbedingungen/) zur Verfügung gestellt
