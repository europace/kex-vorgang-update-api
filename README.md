# KEX-Vorgang-Update-API

> Die alten Update-Endpunkte sind [hier](#legacy-update-apis) dokumentiert.

## Allgemeines

Schnittstelle für das Ändern von KreditSmart-Vorgängen.  
Alle hier dokumentierten Schnittstellen sind [GraphQL-Schnittstellen](https://docs.api.europace.de/privatkredit/graphql/).  

Die URL ist https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge

> ⚠️ Diese Schnittstelle wird kontinuierlich weiterentwickelt. Daher erwarten wir
> von allen Nutzern dieser Schnittstelle, dass sie das "[Tolerant Reader Pattern](https://martinfowler.com/bliki/TolerantReader.html)" nutzen, d.h.
> tolerant gegenüber kompatiblen API-Änderungen beim Lesen und Prozessieren der Daten sind:
>
> 1. unbekannte Felder dürfen keine Fehler verursachen
>
> 2. Strings mit eingeschränktem Wertebereich (Enums) müssen mit neuen, unbekannten Werten umgehen können
>
> 3. sinnvoller Umgang mit HTTP-Statuscodes, die nicht explizit dokumentiert sind
>

<!-- https://opensource.zalando.com/restful-api-guidelines/#108 -->

### Authentifizierung

Diese API ist mit [OAuth](https://docs.api.europace.de/common/authentifizierung/authorization-api/) besichert. 
Damit der Client für diese API genutzt werden kann, muss im Partnermanagement die Berechtigung **KreditSmart-Vorgänge anlegen/verändern** (Scope `privatkredit:vorgang:schreiben`) aktiviert sein.

### Fehlercodes

Die Besonderheit in GraphQL ist u.a., dass die meisten Fehler nicht über HTTP-Fehlercodes wiedergegeben werden. In vielen Fällen bekommt man einen Status 200 zurück, obwohl ein Fehler aufgetreten ist.
Dafür gibt es das Attribut `errors` in der Response. Weitere Infos gibt es [hier](https://docs.api.europace.de/privatkredit/graphql/)

#### HTTP-Status Errors

| Fehlercode | Nachricht             | Erklärung                                                                                                   |
|------------|-----------------------|-------------------------------------------------------------------------------------------------------------|
| 401        | Unauthorized          | Authentifizierung ist fehlgeschlagen                                                                        |
| 403        | Forbidden             | Der API-Client besitzt einen falschen Scope                                                                 |
| 415        | Unsupported MediaType | Es wurde ein falscher content-type angegeben

#### GraphQL Errors

| Fehlercode | Nachricht             | Erklärung                                                                                                   |
|------------|-----------------------|-------------------------------------------------------------------------------------------------------------|
| 400        | Bad Request           | Request Format ist ungültig, z.B. Pflichtfelder fehlen, Parameternamen, -typen oder -werte sind falsch, ... |
| 403        | Forbidden             | Der authentifizierte Nutzer besitzt nicht die nötigen Rechte                                                |
| 404        | NOT FOUND             | Der Vorgang existiert nicht                                                                                 |
| 410        | GONE                  | Der Vorgang wurde zwischenzeitlich gelöscht                                                                 |

## Antragstellerdaten anpassen

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Die `antragstellerId` muss in dem Vorgang vorhanden sein und auf einen bereits vorhandenen Antragsteller referenzieren.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Wenn Felder, die keinen Default Wert besitzen, nicht angegeben werden, werden die vorigen Werte entfernt.

### Mutationen

**updatePersonendaten** ( vorgangsnummer: String!, antragstellerId: String!, personendaten: [Personendaten](#personendaten)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Personendaten](#personendaten) für einen Antragsteller eines Vorgangs anpassen.

**updateWohnsituation** ( vorgangsnummer: String!, antragstellerId: String!, wohnsituation: [Wohnsituation](#wohnsituation)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Wohnsituation](#wohnsituation) für einen Antragsteller eines Vorgangs anpassen.

**updateHerkunft** ( vorgangsnummer: String!, antragstellerId: String!, herkunft: [Herkunft](#herkunft)!  ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Herkunft](#herkunft) für einen Antragsteller eines Vorgangs anpassen.

**updateBeschaeftigung** ( vorgangsnummer: String!, antragstellerId: String!, beschaeftigung: [Beschaeftigung](#beschaeftigung)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man die [Beschaeftigung](#beschaeftigung) zu einem Antragsteller eines Vorgangs anpassen.
>  
> Hinweise:  
> * Die [Beschaeftigung](#beschaeftigung) berücksichtigt genau eine Beschäftigungsart und nutzt dann das dazu korrespondierende Feld für die Aktualisierung.

## Haushaltspositionen anpassen

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Die in dem Feld `antragstellerIds` verwendeten IDs müssen in dem Vorgang vorhanden sein und auf bereits vorhandene Antragsteller referenzieren.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Wenn Felder, die keinen Default Wert besitzen, nicht angegeben werden, werden die vorigen Werte entfernt.
* Die im Feld `id` verwendete ID muss eine existierend Haushaltsposition vom entsprechenden Typ referenzieren.

### Immobilie anpassen

**addImmobilie** ( vorgangsnummer: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicCreatedResponse](#basiccreatedresponse)!

> Eine Immobilie einem Vorgang hinzufügen. Die Response enthält die `id` der angelegten Immobilie. Diese `id` kann als Referenz für weiter Änderungen benutzt werden.

**updateImmobilie** ( vorgangsnummer: String!, id: String!, immobilie: [Immobilie](#immobilie)! ) -> [BasicResponse](#basicresponse)!

> Eine existierende Immobilie ändern. Die Immobilie wird per `id` referenziert.

**deleteImmobilie** ( vorgangsnummer: String!, id: String!) -> [BasicResponse](#basicresponse)!

> Eine existierende Immobilie löschen. Die Immobilie wird per `id` referenziert.

## Finanzbedarf anpassen

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Wenn Felder, die keinen Default Wert besitzen, nicht angegeben werden, werden die vorigen Werte entfernt.

### Mutationen 

**updateFinanzierungswunsch** ( vorgangsnummer: String!, finanzierungswunsch: [Finanzierungswunsch](#finanzierungswunsch)! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man den [Finanzierungswunsch](#finanzierungswunsch) eines Vorgangs anpassen.
>
> Hinweise:
> * Das Feld `Finanzierungswunsch.rateMonatlich` wird nur berücksichtigt, wenn keine `laufzeitInMonaten` angegeben ist.
> * Wenn das Feld `Finanzierungswunsch.ratenzahlungstermin` nicht angegeben wird, wird der Wert `MONATSENDE` verwendet.

## Vorgangsdaten anpassen

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.

### Mutationen 

**addKommentare** ( vorgangsnummer: String!, kommentare: [String!]! ) -> [BasicResponse](#basicresponse)!

> Mit dieser Mutation kann man ein oder mehrere Kommentare zu einem Vorgang hinzufügen.
>  
> Hinweise:
> * Leere Strings werden ignoriert und nicht als Kommentar importiert.

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

## Legacy-Update-APIs

> ⚠️ Diese APIs sind deprecated. Sie werden nicht mehr weiterentwickelt und nur noch supported, bis eine Alternative zur Verfügung steht.

### Dokumente

Diese Schnittstelle ermöglicht das automatisierte Importieren von Dokumenten in einen existierenden **Kredit**Smart-Vorgang.

Dokumente können per **HTTP POST** importiert werden.

Die URL ist:

    https://www.europace2.de/kreditsmart/kex/vorgang/{vorgangsnummer}/dokument

Die Daten werden als JSON im Body des POST Requests übermittelt.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **201 CREATED**.

#### Request Format

Die Angaben werden als JSON im Body des Requests gesendet.

	{
		"filename": String,
		"base64Content": String
	}

Beide Felder müssen befüllt sein um das Dokument erfolgreich importieren zu können.

##### Request Beispiel:

```bash
curl -X POST \
  https://www.europace2.de/kreditsmart/kex/vorgang/123456/dokument \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer xxxxxxx' \
  -d '{
	"filename": "Test.pdf",
	"base64Content": "JVBERi0xLjMKJcTl8uXrp"
}'
```

## Nutzungsbedingungen

Die APIs werden unter folgenden [Nutzungsbedingungen](https://docs.api.europace.de/nutzungsbedingungen/) zur Verfügung gestellt
