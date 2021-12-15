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

## Update data of Antragsteller

### Hints

* The Vorgang has to be aktiv, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* The `antragstellerId` has correspond to an Antragsteller in the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* Values of fields, which do not have a default value and are not specified in the request, will be deleted.

### Update Personendaten

**updatePersonendaten** ( vorgangsnummer: String!, antragstellerId: String!, personendaten: [Personendaten](#personendaten)! ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Personendaten](#personendaten) for an Antragsteller of a Vorgang.

#### Example

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

### Update Wohnsituation

**updateWohnsituation** ( vorgangsnummer: String!, antragstellerId: String!, wohnsituation: [Wohnsituation](#wohnsituation)! ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Wohnsituation](#wohnsituation) for an Antragsteller of a Vorgang.

#### Example

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

### Update Herkunft

**updateHerkunft** ( vorgangsnummer: String!, antragstellerId: String!, herkunft: [Herkunft](#herkunft)!  ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Herkunft](#herkunft) for an Antragsteller of a Vorgang.

#### Example

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

### Update Beschäftigung

**updateBeschaeftigung** ( vorgangsnummer: String!, antragstellerId: String!, beschaeftigung: [Beschaeftigung](#beschaeftigung)! ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Beschaeftigung](#beschaeftigung) for an Antragsteller of a Vorgang.

#### Hints

* The [Beschaeftigung](#beschaeftigung) is only taking one Beschäftigungsart into acocunt and uses only the corresponding fields for the update.

#### Example

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

### Hints

* The Vorgang has to be aktiv, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* Every entry in `antragstellerIds` has to correspond to an Antragsteller in the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* Values of fields, which do not have a default value and are not specified in the request, will be deleted.
* The value of the field `id` has to correspond to a Haushaltsposition in the Vorgang with the corresponding type.

### Einkunft aus Nebentaetigkeit anpassen

#### Hints

* The Haushaltsposition `Einkunft aus einer Nebentätigkeit` can only be related to 1 Antragsteller. If you provide more than 1 `antragstellerId` you will receive a GraphQL error with the error code 422 - UNPROCESSABLE
  ENTITY.

**addEinkunftAusNebentaetigkeit** ( vorgangsnummer String!, einkunftAusNebentaetigkeit [EinkunftAusNebentaetigkeit](#einkunftausnebentaetigkeit)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Einkunft aus einer Nebentätigkeit to a Vorgang. The Response contains the `id` of the creates Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateEinkunftAusNebentaetigkeit** ( vorgangsnummer: String!, id: String!, einkunftAusNebentaetigkeit [EinkunftAusNebentaetigkeit](#einkunftausnebentaetigkeit)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Einkunft aus einer Nebentätigkeit. The Haushaltsposition is referenced by the `id`.

**deleteEinkunftAusNebentaetigkeit** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Einkunft aus einer Nebentätigkeit. The Haushaltsposition is referenced by the `id`.

### Immobilie anpassen

**addImmobilie** ( vorgangsnummer: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add an Immobilie to a Vorgang. The Response contains the `id` of the creates Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateImmobilie** ( vorgangsnummer: String!, id: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Immobilie. The Haushaltsposition is referenced by the `id`.

**deleteImmobilie** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Immobilie. The Haushaltsposition is referenced by the `id`.

### Mietausgabe anpassen

**addMietausgabe** ( vorgangsnummer String!, mietausgabe [Mietausgabe](#mietausgabe)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Mietausgabe to a Vorgang. The Response contains the `id` of the creates Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateMietausgabe** ( vorgangsnummer: String!, id: String!, mietausgabe [Mietausgabe](#mietausgabe)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Mietausgabe. The Haushaltsposition is referenced by the `id`.

**deleteMietausgabe** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Mietausgabe. The Haushaltsposition is referenced by the `id`.

### Private Krankenversicherung anpassen

#### Hints

* The Haushaltsposition `private Krankenversicherung` can only be related to 1 Antragsteller. If you provide more than 1 `antragstellerId` you will receive a GraphQL error with the error code 422 - UNPROCESSABLE
  ENTITY.

**addPrivateKrankenversicherung** ( vorgangsnummer String!, privateKrankenversicherung [PrivateKrankenversicherung](#private-krankenversicherung)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a private Krankenversicherung to a Vorgang. The Response contains the `id` of the creates Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updatePrivateKrankenversicherung** ( vorgangsnummer: String!, id: String!, privateKrankenversicherung [PrivateKrankenversicherung](#private-krankenversicherung)! )
-> [BasicResponse](#basicresponse)!

> Update an existing private Krankenversicherung. The Haushaltsposition is referenced by the `id`.

**deletePrivateKrankenversicherung** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing private Krankenversicherung. The Haushaltsposition is referenced by the `id`.

## Finanzierungswunsch anpassen

**updateFinanzierungswunsch** ( vorgangsnummer: String!, finanzierungswunsch: [Finanzierungswunsch](#finanzierungswunsch)! ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Finanzierungswunsch](#finanzierungswunsch) of a Vorgang.

### Hints

* The Vorgang has to be aktiv, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* The field `Finanzierungswunsch.rateMonatlich` is only processed, if the field `laufzeitInMonaten` is not set.
* If the field `Finanzierungswunsch.ratenzahlungstermin` is not specified, the default value `MONATSENDE` is used.
* Values of fields, which do not have a default value and are not specified in the request, will be deleted.

### Example

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

> This mutation is for adding one or more Kommentare to a Vorgang.

### Hints

* The Vorgang has to be aktiv, which means it must not be archived.
* The authenticated user has to have Übernahmerechte to the Kundenbetreuer.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* Empty Strings will be ignored.

### Example

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

The `Beschaeftigungsart` determines which data is used. For example the `beschaeftigungsart=ARBEITER` means that all data of field `arbeiter` is used, for `beschaeftigungsart=BEAMTER` the data of field `beamter` is used. All other fields will be ignored.
If there is no value for `Beschaeftigungsart` or the corresponding field to a `Beschaeftigungsart` is empty, all data is ignored.

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

This Type uses the format [ISO-3166/ALPHA-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements)

In addition there is the value "SONSTIGE" ("other")

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

> If we do server-side adapions of the data to ensure a valid dataset, we add hints to the `messages` field of the response. These are purely to inform the client, they are NEVER any errors in the `messages` field. These will be shown in the `errors` field instead.

### BasicResponse

    {
        "messages" [ String ]
    }

### BasicCreatedResponse

    {
        "messages": [ String ]
        "id": String
    }

## Terms of use
The APIs are made available under the following [Terms of Use](https://docs.api.europace.de/terms/).
