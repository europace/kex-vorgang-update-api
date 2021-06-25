# KEX-Vorgang-Update-API

> ⚠️ Die neue API befindet sich gerade noch im Aufbau und kann daher jederzeit größere Änderungen beinhalten.  
> Die alten Update-Endpunkte sind [hier](#legacy) dokumentiert.


## Allgemeines

Schnittstelle für das Ändern von KreditSmart-Vorgängen.  
Alle hier dokumentierten Schnittstellen sind [GraphQL-Schnittstellen](https://docs.api.europace.de/privatkredit/graphql/).
Die URL ist:

    https://kex-vorgaenge.ratenkredit.api.europace.de/vorgaenge

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

Für jeden Request ist eine Authentifizierung erforderlich. Die Authentifizierung erfolgt über den OAuth 2.0 Client-Credentials Flow.

| Request Header Name | Beschreibung           |
|---------------------|------------------------|
| Authorization       | OAuth 2.0 Bearer Token |


Das Bearer Token kann über die [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/) angefordert werden.
Dazu wird ein Client benötigt der vorher von einer berechtigten Person über das Partnermanagement angelegt wurde.
Eine Anleitung dafür befindet sich im [Help Center](https://europace2.zendesk.com/hc/de/articles/360012514780).

Damit der Client für diese API genutzt werden kann, muss im Partnermanagement die Berechtigung **KreditSmart-Vorgänge anlegen/verändern** (Scope `privatkredit:vorgang:schreiben`) aktiviert sein.


Schlägt die Authentifizierung fehl, erhält der Aufrufer eine HTTP Response mit Statuscode **401 UNAUTHORIZED**.


### Nachverfolgbarkeit von Requests

Für jeden Request soll eine eindeutige ID generiert werden, die den Request im EUROPACE System nachverfolgbar macht und so bei etwaigen Problemen oder Fehlern die systemübergreifende Analyse erleichtert.  
Die Übermittlung der X-TraceId erfolgt über einen HTTP-Header. Dieser Header ist optional.
Wenn er nicht gesetzt ist, wird eine ID vom System generiert.
Hilfreich für die Analyse ist es, wenn die TraceId mit einem System-Kürzel beginnt (im Beispiel unten 'sys').

| Request Header Name | Beschreibung                    | Beispiel    |
|---------------------|---------------------------------|-------------|
| X-TraceId           | eindeutige ID für jeden Request | sys12345678 |

### GraphQL-Requests

Die Angaben werden als JSON mit UTF-8 Encoding im Body des Requests gesendet.
Die Attribute innerhalb eines Blocks können dabei in beliebiger Reihenfolge angegeben werden.

Die Schnittstelle unterstützt alle gängigen GraphQL Formate, genaueres kann man z.B. unter [https://graphql.org/learn/queries/](https://graphql.org/learn/queries/) nachlesen.

Im Body des Requests wird die GraphQL-Query als String im Property `query` mitgeschickt. Falls die Query
Parameter enthält, können diese Werte direkt in der Query gesetzt werden oder es können in der Query
Variablen definiert werden, deren konkrete Werte dann im Property `variables` als Key-Value-Map übermittelt werden.
In unseren Beispielen nutzen wir die Notation mit Variablen.

    {
      "query": "...",
      "variables": { ... }
    }

### Fehlercodes

Die Besonderheit in GraphQL ist u.a., dass die meisten Fehler nicht über HTTP-Fehlercodes wiedergegeben werden.
In vielen Fällen bekommt man einen Status 200 zurück, obwohl ein Fehler aufgetreten ist. Dafür gibt es das Attribut `errors` in der Response.
Weitere Infos gibt es [hier](https://docs.api.europace.de/privatkredit/graphql/)

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

## Finanzierungswunsch anpassen

Mit der Mutation `updateFinanzierungswunsch` kann man den [Finanzierungswunsch](#finanzierungswunsch) eines Vorgangs anpassen.

### Hinweise

* Der Vorgang muss aktiv, d.h. nicht archiviert, sein.
* Der authentifizierte Nutzer muss zum Zeitpunkt des Updates der Bearbeiter des Vorgangs sein.
* Der Datenkontext (TESTUMGEBUNG|ECHTGESCHAEFT) muss zum Zeitpunkt des Updates für den authentifizierten Nutzer erlaubt sein.
* Das Feld `Finanzierungswunsch.rateMonatlich` wird nur berücksichtigt, wenn keine `laufzeitInMonaten` angegeben ist.
* Wenn das Feld `Finanzierungswunsch.ratenzahlungstermin` leer ist, wird hier `MONATSENDE` als Defaultwert gesetzt.

### Request

| Parametername       | Typ                                          | Default         | Bemerkung                     |
|---------------------|----------------------------------------------|-----------------|-------------------------------|
| vorgangsnummer      | String!                                      | - (Pflichtfeld) |                               |
| finanzierungswunsch | [Finanzierungswunsch](#finanzierungswunsch)! | - (Pflichtfeld) | Leere Felder löschen den Wert |

### Response

Diese Mutation liefert als Rückgabewert eine Liste von Meldungen.

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
        laufzeitInMonaten: Integer
        ratenzahlungstermin: Ratenzahlungstermin
        provisionswunschInProzent: BigDecimal
        kreditbetrag: BigDecimal
        rateMonatlich: BigDecimal
    }

#### Ratenzahlungstermin

    {
        MONATSENDE
        MONATSMITTE
    }

## Response-Datentypen

### Meldungen (Liste von Strings)

Wenn Daten angepasst werden müssen, um eine valide Verarbeitung zu gewährleisten, werden diese Anpassungen als Meldungen zurückgegeben.

## Legacy-Update-APIs

> ⚠️ Diese APIs sind deprecated. Sie werden nicht mehr weiterentwickelt und nur noch supported, bis eine Alternative zur Verfügung steht.

### Dokumente

Diese Schnittstelle ermöglicht das automatisierte Importieren von Dokumenten in
einen existierenden **Kredit**Smart-Vorgang.

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


### Kommentare

Diese Schnittstelle ermöglicht das automatisierte Importieren von Kommentaren in
einen existierenden **Kredit**Smart-Vorgang.

Kommentare können per **HTTP POST** importiert werden.

Die URL ist:

    https://www.europace2.de/kreditsmart/kex/vorgang/{vorgangsnummer}/kommentare

Die Daten werden als JSON im Body des POST Requests übermittelt.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **200 OK**.

#### Request Format

Die Kommentare werden als JSON im Body des Requests gesendet.
Es kann eine Liste von Strings übermittelt werden. Jedes Element der Liste erzeugt einen neuen Kommentar.

	[ <Kommentar> ]


##### Request Beispiel:

```bash
curl -X POST \
  https://www.europace2.de/kreditsmart/kex/vorgang/123456/kommentare \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer xxxxxxx' \
  -d '["Testkommentar"]'
  ```

## Nutzungsbedingungen
Die APIs werden unter folgenden [Nutzungsbedingungen](https://docs.api.europace.de/nutzungsbedingungen/) zur Verfügung gestellt
