# KEX-Vorgang-Update-API

> ⚠️ You'll find German domain-specific terms in the documentation, for translations and further explanations please refer to our [glossary](https://docs.api.europace.de/common/glossary/)

## General

APIs for updating data in KreditSmart-Vorgängen. The URL for this service is:

    https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge

All APIs documented here are [GraphQL-APIs](https://docs.api.europace.de/privatkredit/graphql/).

> ⚠️ This API is continuously developed. Therefore we expect
> all users to align with the "[Tolerant Reader Pattern](https://martinfowler.com/bliki/TolerantReader.html)", which requires clients to be
> tolerant towards compatible API changes when reading and processing the data. This means:
>
> 1. unknown properties must not result in errors
>
> 2. Strings with a restricted set of values (Enums) must support new unknown values
>
> 3. sensible usage of HTTP status codes, even if they are not explicitly documented
>

<!-- https://opensource.zalando.com/restful-api-guidelines/#108 -->

### Authentication

These APIs are secured by the OAuth 2.0 client credentials flow using the [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/). To use these APIs your OAuth2-Client needs
the following scopes:

| Scope                          | Label in Partnermanagement             | Description                  |
|--------------------------------|----------------------------------------|------------------------------|
| privatkredit:vorgang:schreiben | KreditSmart-Vorgänge anlegen/verändern | Scope for updating a Vorgang |

### GraphQL-Requests

These APIs accept data with the content-type **application/json** with UTF-8 encoding. The fields inside a block can be sent in any order.

The APIs support all common GraphQL formats. More information can be found at [https://graphql.org/learn/queries/](https://graphql.org/learn/queries/).

The body of a GraphQL request contains the field `query`, which includes the GraphQL query as a String. Parameters can be set directly in the query or defined as variables. The variables can be sent
in the `variables` field of the body as a key-value map. All our examples use variables.

    {
      "query": "...",
      "variables": { ... }
    }

### Error Codes

One of the special features in GraphQL is that most errors are not reflected via HTTP error codes. In many cases you receive a status code 200, even though an error has occurred. These GraphQL errors
can be found in the `errors` field of the response body. More information about error codes can be found [here](https://docs.api.europace.de/privatkredit/graphql/#error-handling).

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

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* The `antragstellerId` has to refer to an existing Antragsteller in the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* Values of fields, which do not have a default value and are not specified in the request, will be deleted.

### Update Antragsteller

**addAntragsteller** ( vorgangsnummer: String!, antragsteller: [Antragsteller](#antragsteller)! ) -> [BasicResponse](#basicresponse)!

> Add an Antragsteller to a Vorgang. The Response contains the `id` of the created Antragsteller. This `id` can be used to update or delete this Antragsteller.

#### Hints

* A Vorgang can have at most two Antragsteller.
* All fields within the Antragsteller are optional, if they are omitted an empty Antragsteller is created.

**deleteAntragsteller** ( vorgangsnummer: String!, id: String! ) -> [BasicResponse](#basicresponse)!

> Delete an existing Antragsteller. The Antragsteller is referenced by the `id`.

#### Hints

* A Vorgang must always have at least one Antragsteller.

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

* The [Beschaeftigung](#beschaeftigung) is only taking one Beschäftigungsart into account and uses only the corresponding fields for the update.

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

## Update Haushaltspositionen

### Hints

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* Every entry in `antragstellerIds` has to correspond to an Antragsteller in the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* Values of fields, which do not have a default value and are not specified in the request, will be deleted.
* The value of the field `id` has to correspond to a Haushaltsposition in the Vorgang with the corresponding type.

### Update Bausparvertrag

**addBausparvertrag** ( vorgangsnummer String!, bausparvertrag [Bausparvertrag](#bausparvertrag)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Bausparvertrag to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateBausparvertrag** ( vorgangsnummer: String!, id: String!, bausparvertrag [Bausparvertrag](#bausparvertrag)! ) -> [BasicResponse](#basicresponse)!

> Update a existing Bausparvertrag. The Haushaltsposition is referenced by the `id`.

**deleteBausparvertrag**( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete a existing Bausparvertrag. The Haushaltsposition is referenced by the `id`.

### Update Dispositionskredit

#### Hints

* If the `finanzierungszweck` of the Vorgang is `FAHRZEUGKAUF` then this position cannot be refinanced. This means that `abloesen` cannot be `true` and will be set to `false` if it is. Related fields
  will not be saved.
  Related fields are `bic` and `iban`.

* If `iban` or `bic` are invalid, the value will be ignored and set to `null`.

**addDispositionskredit** ( vorgangsnummer String!, dispositionskredit [Dispositionskredit](#dispositionskredit)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Dispositionskredit to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateDispositionskredit** ( vorgangsnummer: String!, id: String!, dispositionskredit [Dispositionskredit](#dispositionskredit)! )
-> [BasicResponse](#basicresponse)!

> Update an existing Dispositionskredit. The Haushaltsposition is referenced by the `id`.

**deleteDispositionskredit** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Dispositionskredit. The Haushaltsposition is referenced by the `id`.

### Update Ehegattenunterhalt

#### Hints

* The Haushaltsposition `ehegattenunterhalt` can only be related to one Antragsteller. If you provide more than one `antragstellerId` you will receive a GraphQL error with the error
  code `422 - UNPROCESSABLE ENTITY`.

**addEhegattenunterhalt** ( vorgangsnummer String!, ehegattenunterhalt [Ehegattenunterhalt](#ehegattenunterhalt)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add an Ehegattenunterhalt to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateEhegattenunterhalt** ( vorgangsnummer: String!, id: String!, ehegattenunterhalt [Ehegattenunterhalt](#ehegattenunterhalt)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Ehegattenunterhalt. The Haushaltsposition is referenced by the `id`.

**deleteEhegattenunterhalt** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Ehegattenunterhalt. The Haushaltsposition is referenced by the `id`.

### Update Einkunft aus Nebentaetigkeit

#### Hints

* The Haushaltsposition `einkunftAusNebentätigkeit` can only be related to one Antragsteller. If you provide more than one `antragstellerId` you will receive a GraphQL error with the error
  code `422 - UNPROCESSABLE ENTITY`.

**addEinkunftAusNebentaetigkeit** ( vorgangsnummer String!, einkunftAusNebentaetigkeit [EinkunftAusNebentaetigkeit](#einkunftausnebentaetigkeit)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add an Einkunft aus Nebentätigkeit to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateEinkunftAusNebentaetigkeit** ( vorgangsnummer: String!, id: String!, einkunftAusNebentaetigkeit [EinkunftAusNebentaetigkeit](#einkunftausnebentaetigkeit)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Einkunft aus Nebentätigkeit. The Haushaltsposition is referenced by the `id`.

**deleteEinkunftAusNebentaetigkeit** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Einkunft aus Nebentätigkeit. The Haushaltsposition is referenced by the `id`.

### Update Immobilie

**addImmobilie** ( vorgangsnummer: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add an Immobilie to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateImmobilie** ( vorgangsnummer: String!, id: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Immobilie. The Haushaltsposition is referenced by the `id`.

**deleteImmobilie** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Immobilie. The Haushaltsposition is referenced by the `id`.

### Update Kind

**addKind** ( vorgangsnummer String!, kind [Kind](#kind)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Kind to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateKind** ( vorgangsnummer: String!, id: String!, kind [Kind](#kind)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Kind. The Haushaltsposition is referenced by the `id`.

**deleteKind** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Kind. The Haushaltsposition is referenced by the `id`.

### Update Kontoverbindung

**updateKontoverbindung** ( vorgangsnummer: String!, kontoverbindung [Kontoverbindung](#kontoverbindung)! ) -> [BasicResponse](#basicresponse)!

> Update the Kontoverbindung.

#### Hints

* If `iban` or `bic` are invalid, the value will be ignored and set to `null`.

### Update Kreditkarte

#### Hints

* If the `finanzierungszweck` of the Vorgang is `FAHRZEUGKAUF` then this position cannot be refinanced. This means that `abloesen` cannot be `true` and will be set to `false` if it is. Related fields
  will not be saved.
  Related fields are `bic` and `iban`.

* If `iban` or `bic` are invalid, the value will be ignored and set to `null`.

**addKreditkarte** ( vorgangsnummer String!, kreditkarte [Kreditkarte](#kreditkarte)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Kreditkarte to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateKreditkarte** ( vorgangsnummer: String!, id: String!, kreditkarte [Kreditkarte](#kreditkarte)! )
-> [BasicResponse](#basicresponse)!

> Update an existing Kreditkarte. The Haushaltsposition is referenced by the `id`.

**deleteKreditkarte** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Kreditkarte. The Haushaltsposition is referenced by the `id`.

### Update Leasing

**addLeasing** ( vorgangsnummer String!, leasing [Leasing](#leasing)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Leasing to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateLeasing** ( vorgangsnummer: String!, id: String!, leasing [Leasing](#leasing)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Leasing. The Haushaltsposition is referenced by the `id`.

**deleteLeasing** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Leasing. The Haushaltsposition is referenced by the `id`.

### Update Lebensversicherung

**Lebensversicherung** ( vorgangsnummer String!, lebensversicherung [Lebensversicherung](#lebensversicherung)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Lebensversicherung to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateLebensversicherung** ( vorgangsnummer: String!, id: String!, lebensversicherung [Lebensversicherung](#lebensversicherung)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Lebensversicherung. The Haushaltsposition is referenced by the `id`.

**deleteLebensversicherung** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Lebensversicherung. The Haushaltsposition is referenced by the `id`.

### Update Mietausgabe

**addMietausgabe** ( vorgangsnummer String!, mietausgabe [Mietausgabe](#mietausgabe)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a Mietausgabe to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateMietausgabe** ( vorgangsnummer: String!, id: String!, mietausgabe [Mietausgabe](#mietausgabe)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Mietausgabe. The Haushaltsposition is referenced by the `id`.

**deleteMietausgabe** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Mietausgabe. The Haushaltsposition is referenced by the `id`.

### Update Private Krankenversicherung

#### Hints

* The Haushaltsposition `privateKrankenversicherung` can only be related to one Antragsteller. If you provide more than one `antragstellerId` you will receive a GraphQL error with the error code `422 - UNPROCESSABLE ENTITY`.

**addPrivateKrankenversicherung** ( vorgangsnummer String!, privateKrankenversicherung [PrivateKrankenversicherung](#privatekrankenversicherung)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a private Krankenversicherung to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updatePrivateKrankenversicherung** ( vorgangsnummer: String!, id: String!, privateKrankenversicherung [PrivateKrankenversicherung](#privatekrankenversicherung)! )
-> [BasicResponse](#basicresponse)!

> Update an existing private Krankenversicherung. The Haushaltsposition is referenced by the `id`.

**deletePrivateKrankenversicherung** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing private Krankenversicherung. The Haushaltsposition is referenced by the `id`.

### Update Ratenkredit

#### Hints

* If the `finanzierungszweck` of the Vorgang is `FAHRZEUGKAUF` then this position cannot be refinanced. This means that `abloesen` cannot be `true` and will be set to `false` if it is. Related fields
  will not be saved.
  Related fields are `bic`, `datumErsteZahlung`, `iban`, and `urspruenglicherKreditbetrag`.

* If `iban` or `bic` are invalid, the value will be ignored and set to `null`.

**addRatenkredit** ( vorgangsnummer String!, ratenkredit [Ratenkredit](#ratenkredit)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a ratenkredit to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateRatenkredit** ( vorgangsnummer: String!, id: String!, ratenkredit [Ratenkredit](#ratenkredit)! )
-> [BasicResponse](#basicresponse)!

> Update an existing ratenkredit. The Haushaltsposition is referenced by the `id`.

**deleteRatenkredit** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing ratenkredit. The Haushaltsposition is referenced by the `id`.

### Update Sonstige Ausgabe 

**addSonstigeAusgabe** ( vorgangsnummer String!, sonstigeAusgabe [SonstigeAusgabe](#sonstigeausgabe)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a sonstige Ausgabe to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateSonstigeAusgabe** ( vorgangsnummer: String!, id: String!, sonstigeAusgabe [SonstigeAusgabe](#sonstigeausgabe)! ) -> [BasicResponse](#basicresponse)!

> Update an existing sonstige Ausgabe. The Haushaltsposition is referenced by the `id`.

**deleteSonstigeAusgabe** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing sonstige Ausgabe. The Haushaltsposition is referenced by the `id`.

### Update Sonstige Einnahme

#### Hints

* The Haushaltsposition `sonstigeEinnahme` can only be related to one Antragsteller. If you provide more than one `antragstellerId` you will receive a GraphQL error with the error
  code `422 - UNPROCESSABLE ENTITY`.

**addSonstigeEinnahme** ( vorgangsnummer String!, sonstigeEinnahme [SonstigeEinnahme](#sonstigeeinnahme)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a sonstige Einnahme to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateSonstigeEinnahme** ( vorgangsnummer: String!, id: String!, sonstigeEinnahme [SonstigeEinnahme](#sonstigeeinnahme)! ) -> [BasicResponse](#basicresponse)!

> Update an existing sonstige Einnahme. The Haushaltsposition is referenced by the `id`.

**deleteSonstigeEinnahme** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing sonstige Einnahme. The Haushaltsposition is referenced by the `id`.

### Update Sonstige Verbindlichkeit

#### Hints

* If the `finanzierungszweck` of the Vorgang is `FAHRZEUGKAUF` then this position cannot be refinanced. This means that `abloesen` cannot be `true` and will be set to `false` if it is. Related fields
  will not be saved.
  Related fields are `bic`, `datumErsteZahlung`, `iban`, and `urspruenglicherKreditbetrag`.

* If `iban` or `bic` are invalid, the value will be ignored and set to `null`.

**addSonstigeVerbindlichkeit** ( vorgangsnummer String!, sonstigeVerbindlichkeit [SonstigeVerbindlichkeit](#sonstigeverbindlichkeit)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add a sonstige Verbindlichkeit to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateSonstigeVerbindlichkeit** ( vorgangsnummer: String!, id: String!, sonstigeVerbindlichkeit [SonstigeVerbindlichkeit](#sonstigeverbindlichkeit)! )
-> [BasicResponse](#basicresponse)!

> Update an existing sonstige Verbindlichkeit. The Haushaltsposition is referenced by the `id`.

**deleteSonstigeVerbindlichkeit** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing sonstige Verbindlichkeit. The Haushaltsposition is referenced by the `id`.

### Update Unbefristete Zusatzrente

#### Hints

* The Haushaltsposition `unbefristeteZusatzrente` can only be related to one Antragsteller. If you provide more than one `antragstellerId` you will receive a GraphQL error with the error
  code `422 - UNPROCESSABLE ENTITY`.

**addUnbefristeteZusatzrente** ( vorgangsnummer String!, unbefristeteZusatzrente [UnbefristeteZusatzrente](#unbefristetezusatzrente)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add an UnbefristeteZusatzrente to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateUnbefristeteZusatzrente** ( vorgangsnummer: String!, id: String!, unbefristeteZusatzrente [UnbefristeteZusatzrente](#unbefristetezusatzrente)! ) -> [BasicResponse](#basicresponse)!

> Update an existing UnbefristeteZusatzrente. The Haushaltsposition is referenced by the `id`.

**deleteUnbefristeteZusatzrente** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing UnbefristeteZusatzrente. The Haushaltsposition is referenced by the `id`.

### Update Unterhaltsverpflichtung

#### Hints

* The Haushaltsposition `unterhaltsverpflichtung` can only be related to one Antragsteller. If you provide more than one `antragstellerId` you will receive a GraphQL error with the error
  code `422 - UNPROCESSABLE ENTITY`.

**addUnterhaltsverpflichtung** ( vorgangsnummer String!, unterhaltsverpflichtung [Unterhaltsverpflichtung](#unterhaltsverpflichtung)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Add an Unterhaltsverpflichtung to a Vorgang. The Response contains the `id` of the created Haushaltsposition. This `id` can be used to update or delete this Haushaltsposition.

**updateUnterhaltsverpflichtung** ( vorgangsnummer: String!, id: String!, unterhaltsverpflichtung [Unterhaltsverpflichtung](#unterhaltsverpflichtung)! ) -> [BasicResponse](#basicresponse)!

> Update an existing Unterhaltsverpflichtung. The Haushaltsposition is referenced by the `id`.

**deleteUnterhaltsverpflichtung** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Delete an existing Unterhaltsverpflichtung. The Haushaltsposition is referenced by the `id`.

## Update Finanzbedarf

### Hints

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* Values of fields, which do not have a default value and are not specified in the request, will be deleted.

### Update Fahrzeugkauf

#### Hints

* The `finanzierungszweck` of the Vorgang has to be `FAHRZEUGKAUF` otherwise the update will be ignored.
* Changing the `finanzierungszweck` to something other than `FAHRZEUGKAUF` will delete a previously saved `fahrzeugkauf`.

**updateFahrzeugkauf** ( vorgangsnummer: String!, fahrzeugkauf: [Fahrzeugkauf](#fahrzeugkauf)! ) -> [BasicResponse](#basicresponse)!

> Update the [Fahrzeugkauf](#fahrzeugkauf) of a Vorgang.

### Update Finanzierungswunsch

**updateFinanzierungswunsch** ( vorgangsnummer: String!, finanzierungswunsch: [Finanzierungswunsch](#finanzierungswunsch)! ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Finanzierungswunsch](#finanzierungswunsch) of a Vorgang.

#### Hints

* The field `Finanzierungswunsch.rateMonatlich` is only processed, if the field `laufzeitInMonaten` is not set.
* If the field `Finanzierungswunsch.ratenzahlungstermin` is not specified, the default value `MONATSENDE` is used.

#### Example

##### POST Request

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

##### POST Response

    {
      "data": {
        "messages": []
      },
      "errors": []
    }

### Update Finanzierungszweck

**updateFinanzierungszweck** ( vorgangsnummer: String!, finanzierungszweck: [Finanzierungszweck](#finanzierungszweck)!)

> This mutation is for updating the [Finanzierungszweck](#finanzierungszweck) of a Vorgang.

#### Hints

* The finanzierungszweck cannot be set to `FAHRZEUGKAUF` if the Vorgang has a position which should be refinanced, meaning `abloesen` is true. Relevant positions are [SonstigeVerbindlichkeit](#sonstigeverbindlichkeit), [Kreditkarte](#kreditkarte), [Ratenkredit](#ratenkredit) and [Dispositionskredit](#dispositionskredit).

### Update Ratenschutz

**updateRatenschutz** ( vorgangsnummer: String!, antragstellerId: String!, ratenschutz: [Ratenschutz](#ratenschutz)! ) -> [BasicResponse](#basicresponse)!

> This mutation is for updating the [Ratenschutz](#ratenschutz) for an Antragsteller of a Vorgang.

#### Hints

* The `antragstellerId` has to refer to an existing Antragsteller in the Vorgang.
* The agent must be allowed to sell payment protection insurances.

## Start Account Check

**createAccountCheckSession** ( vorgangsnummer: String! ) -> [AccountCheckSessionResponse](#accountchecksessionresponse)!

> This mutation creates a session for the digital account check provider [tink](https://docs.xs2a.com/). It returns a session key that can be used in their account check wizard.

### Hints

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
* The authenticated user has to be the Bearbeiter of the Vorgang.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.
* The account check can only be used for vorgaenge with one antragsteller.
* The vorgang must contain an IBAN in the kontoverbindung.

## Add Kommentare

**addKommentare** ( vorgangsnummer: String!, kommentare: [String!]! ) -> [BasicResponse](#basicresponse)!

> This mutation is for adding one or more Kommentare to a Vorgang.

### Hints

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
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

## Update Partner

### Update Bearbeiter
**updateBearbeiter** ( vorgangsnummer: String!, partnerId: String! ) -> [BasicResponse](#basicresponse)!

#### Hints

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
* The authenticated user has to have Übernahmerechte to the Kundenbetreuer.
* The partner to be changed to has to have Übernahmerechte to the Kundenbetreuer.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.

### Update Kundenbetreuer
**updateKundenbetreuer** ( vorgangsnummer: String!, partnerId: String! ) -> [BasicResponse](#basicresponse)!

#### Hints

* The Vorgang has to have the status=`AKTIV`, which means it must not be archived.
* The authenticated user has to have Übernahmerechte to the current Kundenbetreuer and to the partner to be changed to.
* The partner to be changed to has to have Übernahmerechte to the current Kundenbetreuer and must be a person.
* The Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) has to be allowed for the authenticated user.

## Request-Datentypen

### Antragsteller

    {
        "herkunft": Herkunft,
        "personendaten": Personendaten,
        "wohnsituation": Wohnsituation,
        "beschaeftigung": Beschaeftigung
    }

### Beschaeftigung

    {
        "beschaeftigungsart": "ANGESTELLTER" | "ARBEITER" | "ARBEITSLOSER" | "BEAMTER" | "FREIBERUFLER" | "HAUSFRAU" | "RENTNER" | "SELBSTSTAENDIGER",
        "angestellter": Angestellter,
        "arbeiter": Arbeiter,              
        "arbeitsloser": Arbeitsloser,
        "beamter": Beamter,
        "freiberufler": Freiberufler,
        "hausfrau": Hausfrau,
        "rentner": Rentner,
        "selbststaendiger": Selbstständiger
    }

The `beschaeftigungsart` determines which data is used. For example the `beschaeftigungsart=ARBEITER` means that all data of field `arbeiter` is used, for `beschaeftigungsart=BEAMTER` the data of
field `beamter` is used. All other fields will be ignored. If there is no value for `beschaeftigungsart` or the corresponding field to a `beschaeftigungsart` is empty, all data is ignored.

#### Angestellter

    {
        "beschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma,   
            "berufsbezeichnung": String,
            "befristung": "BEFRISTET" | "UNBEFRISTET",
            "befristetBis": "YYYY-MM-DD",
            "beschaeftigtSeit": "YYYY-MM-DD",
            "inProbezeit": true | false,
            "nettoeinkommenMonatlich": BigDecimal
        },
        "vorherigesBeschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma,
            "beschaeftigtSeit": "YYYY-MM-DD",
            "beschaeftigtBis": "YYYY-MM-DD"
        }
    }

#### Arbeiter

    {
        "beschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma,   
            "berufsbezeichnung": String,
            "befristung": "BEFRISTET" | "UNBEFRISTET",
            "befristetBis": "YYYY-MM-DD",
            "beschaeftigtSeit": "YYYY-MM-DD",
            "inProbezeit": true | false,
            "nettoeinkommenMonatlich": BigDecimal
        },
        "vorherigesBeschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma,
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
            "arbeitgeber": Firma,
            "berufsbezeichnung": String,
            "beschaeftigtSeit": "YYYY-MM-DD",
            "inProbezeit": true | false,
            "nettoeinkommenMonatlich": BigDecimal,
            "verbeamtetSeit": "YYYY-MM-DD"
        },
        "vorherigesBeschaeftigungsverhaeltnis": {
            "arbeitgeber": Firma,
            "beschaeftigtSeit": "YYYY-MM-DD",
            "beschaeftigtBis": "YYYY-MM-DD"
        }
    }

#### Freiberufler

    {
        "berufsbezeichnung": String,
        "firma": Firma,
        "selbststaendigSeit": "YYYY-MM-DD", 
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
        "rentenversicherung": {
            "name": String,
            "anschrift": Anschrift
        },
        "rentnerSeit": "YYYY-MM-DD",
        "staatlicheRenteMonatlich": BigDecimal
    }

#### Selbstständiger

    {
        "berufsbezeichnung": String,
        "firma": Firma,
        "selbststaendigSeit": "YYYY-MM-DD",
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
        "anschrift": Anschrift,
        "branche": "LANDWIRTSCHAFT_FORSTWIRTSCHAFT_FISCHEREI" | "ENERGIE_WASSERVERSORGUNG_BERGBAU" | "VERARBEITENDES_GEWERBE" | "BAUGEWERBE" | "HANDEL" | "VERKEHR_LOGISTIK" | "INFORMATION_KOMMUNIKATION" | "GEMEINNUETZIGE_ORGANISATION" | "KREDITINSTITUTE_VERSICHERUNGEN" | "PRIVATE_HAUSHALTE" | "DIENSTLEISTUNGEN" | "OEFFENTLICHER_DIENST" | "GEBIETSKOERPERSCHAFTEN" | "HOTEL_GASTRONOMIE" | "ERZIEHUNG_UNTERRICHT" | "KULTUR_SPORT_UNTERHALTUNG" | "GESUNDHEIT_SOZIALWESEN",
        "name": String
    }

### Bausparvertrag

    {
        "antragstellerIds": [ String ],
        "sparbeitragMonatlich": BigDecimal
    }

### Dispositionskredit

    {
        "abloesen": Boolean,
        "antragstellerIds": [String],
        "beanspruchterBetrag": BigDecimal,
        "bic": String,
        "glaeubiger": String,
        "iban": String,
        "verfuegungsrahmen": BigDecimal,
        "zinssatz": BigDecimal,
    }

### Ehegattenunterhalt

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### EinkunftAusNebentaetigkeit

    {
        "antragstellerIds": [ String ],
        "beginnDerTaetigkeit": "YYYY-MM-DD",
        "betragMonatlich": BigDecimal
    }

### Fahrzeugkauf

    {
        "modell": String,
        "marke": String,
        "kaufpreis": BigDecimal,
        "erstzulassungsdatum": "YYYY-MM-DD",
        "laufleistung": Integer,
        "kw": Integer,
        "beglicheneKosten": BigDecimal,
        "ps": Integer,
        "anbieter": "HAENDLER" | "PRIVAT"
    }

### Finanzierungswunsch

    {
        "kreditbetrag": BigDecimal,
        "laufzeitInMonaten": Integer,
        "provisionswunschInProzent": BigDecimal,
        "rateMonatlich": BigDecimal,
        "ratenzahlungstermin": Ratenzahlungstermin
    }

#### Ratenzahlungstermin

        "MONATSENDE" | "MONATSMITTE"

### Finanzierungszweck

        "UMSCHULDUNG" | "FAHRZEUGKAUF" | "MODERNISIERUNG" | "FREIE_VERWENDUNG"

### Herkunft

    {
        "arbeitserlaubnisVorhanden": true | false,
        "arbeitserlaubnisBefristetBis": "YYYY-MM-DD",
        "aufenthaltstitel": "VISUM" | "AUFENTHALTSERLAUBNIS" | "NIEDERLASSUNGSERLAUBNIS" | "ERLAUBNIS_ZUM_DAUERAUFENTHALT_EU",
        "aufenthaltBefristetBis": "YYYY-MM-DD",
        "inDeutschlandSeit": "YYYY-MM-DD",
        "staatsangehoerigkeit": Country,
        "steuerId": String
    }

### Immobilie

    {
        "antragstellerIds": [ String ],
        "bezeichnung": String,
        "darlehen": [
            {
                "restschuld": BigDecimal,
                "zinsbindungBis": "YYYY-MM-DD",
                "rateMonatlich": BigDecimal
            }
        ],
        "immobilienart": "EIGENTUMSWOHNUNG" | "EINFAMILIENHAUS" | "MEHRFAMILIENHAUS" | "BUEROGEBAEUDE",
        "mieteinnahmenKaltMonatlich": BigDecimal,
        "mieteinnahmenWarmMonatlich": BigDecimal,
        "nebenkostenMonatlich": BigDecimal,
        "nutzungsart": "EIGENGENUTZT" | "VERMIETET" | "EIGENGENUTZT_UND_VERMIETET",
        "vermieteteWohnflaeche": Integer,
        "wert": BigDecimal,
        "wohnflaeche": Integer
    }

### Kind

    {
        "antragstellerIds": [ String ],
        "kindergeldFuer": "ERSTES_ODER_ZWEITES_KIND" | "DRITTES_KIND" | "AB_VIERTEM_KIND",
        "name": String,
        "unterhaltseinnahmenMonatlich": BigDecimal
    }

### Kontoverbindung

    {
        "antragstellerIds": [ String ],
        "bic": String,
        "iban": String
    }

### Kreditkarte

    {
        "abloesen": Boolean,
        "antragstellerIds: [String],
        "beanspruchterBetrag": BigDecimal,
        "bic": String,
        "glaeubiger": String,
        "iban": String,
        "rateMonatlich": BigDeciaml,
        "verfuegungsrahmen": BigDecimal,
        "zinssatz": BigDecimal
    }

### Leasing

    {
        "antragstellerIds": [ String ],
        "datumLetzteRate": LocalDate,
        "glaeubiger": String,
        "rateMonatlich": BigDecimal,
        "schlussrate": BigDecimal
    }

### Lebensversicherung

    {
        "antragstellerIds": [ String ],
        "praemieMonatlich": BigDecimal
    }

### Mietausgabe

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
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

### PrivateKrankenversicherung

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### Ratenkredit

    {
        "abloesen": Boolean,
        "antragstellerIds": [ String ],
        "bic": String,
        "datumErsteZahlung": LocalDate,
        "datumLetzteRate": LocalDate,
        "glaeubiger": String,
        "iban": String,
        "rateMonatlich": BigDecimal,
        "restschuld": BigDecimal,
        "schlussrate": BigDeciaml,
        "urspruenglicherKreditbetrag": BigDecimal
    }

### Ratenschutz

    {
        "arbeitslosigkeitAbsicherung": RatenschutzAbsicherung,
        "arbeitsunfaehigkeitAbsicherung": RatenschutzAbsicherung,
        "todesfallAbsicherung": RatenschutzAbsicherung
    }

#### RatenschutzAbsicherung

    {
        "gewuenscht": Boolean,
        "kommentar": String,
        "vorhanden": Boolean,
        "wichtig": Boolean
    }

### SonstigeAusgabe

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### SonstigeEinnahme

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### SonstigeVerbindlichkeit

    {
        "abloesen": Boolean,
        "antragstellerIds": [ String ],
        "bic": String,
        "datumErsteZahlung": LocalDate,
        "datumLetzteRate": LocalDate,
        "glaeubiger": String,
        "iban": String,
        "rateMonatlich": BigDecimal,
        "restschuld": BigDecimal,
        "schlussrate": BigDeciaml,
        "urspruenglicherKreditbetrag": BigDecimal
    }

### UnbefristeteZusatzrente

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### Unterhaltsverpflichtung

    {
        "antragstellerIds": [ String ],
        "betragMonatlich": BigDecimal
    }

### Wohnsituation

    {
        "anschrift": Wohnanschrift,
        "anzahlPersonenImHaushalt": Integer,
        "anzahlPkw": Integer,
        "gemeinsamerHaushalt": true | false,
        "voranschrift": Wohnanschrift,
        "wohnart": "ZUR_MIETE" | "ZUR_UNTERMIETE" | "IM_EIGENEN_HAUS" | "BEI_DEN_ELTERN"
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

> If we do server-side adaptions of the data to ensure a valid dataset, we add hints to the `messages` field of the response. These are purely to inform the client, there are NEVER any errors in
> the `messages` field. Errors will be only shown in the `errors` field of the response.

### BasicResponse

    {
        "messages" [ String ]
    }

### BasicCreatedResponse

    {
        "messages": [ String ]
        "id": String
    }

### AccountCheckSessionResponse

    {
        "wizardSessionKey": String
    }

## Terms of use

The APIs are made available under the following [Terms of Use](https://docs.api.europace.de/terms/).
