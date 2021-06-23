# KEX-Vorgang-Update-API

## Legacy

Dies ist eine Sammlung von verschiedenen Schnittstellen, die es ermöglichen Daten zu einem __existierenden__ Vorgang hinzuzufügen oder zu ändern.

> ⚠️ Diese APIs sind deprecated. Sie werden nicht mehr weiterentwickelt und nur noch supported, bis eine Alternative zur Verfügung steht.

### Authentifizierung

Für jeden Request ist eine Authentifizierung erforderlich. Die Authentifizierung erfolgt über den OAuth 2.0 Client-Credentials Flow.

| Request Header Name | Beschreibung           |
|---------------------|------------------------|
| Authorization       | OAuth 2.0 Bearer Token |


Das Bearer Token kann über die [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/) angefordert werden.
Dazu wird ein Client benötigt, der vorher von einer berechtigten Person über das Partnermanagement angelegt wurde.
Eine Anleitung dafür befindet sich im [Help Center](https://europace2.zendesk.com/hc/de/articles/360012514780).

Damit der Client für diese API genutzt werden kann, muss im Partnermanagement die Berechtigung **KreditSmart-Vorgänge anlegen/verändern** (Scope `privatkredit:vorgang:schreiben`) aktiviert sein.  

Schlägt die Authentifizierung fehl, erhält der Aufrufer eine HTTP Response mit Statuscode **401 UNAUTHORIZED**.

Hat der Client keine Berechtigung die Resource abzurufen, erhält der Aufrufer eine HTTP Response mit Statuscode **403 FORBIDDEN**.


### TraceId zur Nachverfolgbarkeit von Requests

Für jeden Request soll eine eindeutige ID generiert werden, die den Request im EUROPACE 2 System nachverfolgbar macht und so bei etwaigen Problemen oder Fehlern die systemübergreifende Analyse erleichtert.

Die Übermittlung der X-TraceId erfolgt über einen HTTP Header. Dieser Header ist optional,
wenn er nicht gesetzt ist, wird eine ID vom System generiert.  

| Request Header Name | Beschreibung                     | Beispiel   |
|:--------------------|:---------------------------------|:-----------|
| X-TraceId           | eindeutige Id für jeden Request  |sys12345678 |

### Content-Type

Die Schnittstelle akzeptiert Daten mit Content-Type "**application/json**".

Entsprechend muss im Request der Content-Type Header gesetzt werden und zusätzlich das Encoding, wenn es nicht UTF-8 ist.

| Request Header Name | Header Value       |
|:--------------------|:-------------------|
| Content-Type        | application/json |

### Fehlercodes

Wenn der Request nicht erfolgreich verarbeitet werden konnte, liefert die Schnittstelle einen Fehlercode, auf den die aufrufende Anwendung reagieren kann, zurück.

| Fehlercode | Nachricht    | Erklärung                                                                   |
|:-----------|:-------------|:----------------------------------------------------------------------------|
| 400        | Bad Request  | Die Daten entsprechen nicht dem erwarteten Format oder Pflichtfelder fehlen |
| 401        | Unauthorized | Authentifizierung ist fehlgeschlagen                                        |
| 403        | Forbidden    | Autorisierung ist fehlgeschlagen                                            |
| 410        | Gone         | Vorgang ist gelöscht                                                        |

Weitere Fehlercodes und ihre Bedeutung siehe Wikipedia: [HTTP-Statuscode](https://de.wikipedia.org/wiki/HTTP-Statuscode)


### Dokumente

Diese Schnittstelle ermöglicht das automatisierte Importieren von Dokumenten in
einen existierenden **Kredit**Smart-Vorgang.

Dokumente können per **HTTP POST** importiert werden.

Die URL ist:

    https://www.europace2.de/kreditsmart/kex/vorgang/{vorgangsnummer}/dokument

Die Daten werden als JSON im Body des POST Requests übermittelt.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **201 CREATED**.

### Request Format

Die Angaben werden als JSON im Body des Requests gesendet.

	{
		"filename": String,
		"base64Content": String
	}

Beide Felder müssen befüllt sein um das Dokument erfolgreich importieren zu können.

#### Request Beispiel:

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

### Request Format

Die Kommentare werden als JSON im Body des Requests gesendet.
Es kann eine Liste von Strings übermittelt werden. Jedes Element der Liste erzeugt einen neuen Kommentar.

	[ <Kommentar> ]


#### Request Beispiel:

```bash
curl -X POST \
  https://www.europace2.de/kreditsmart/kex/vorgang/123456/kommentare \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer xxxxxxx' \
  -d '["Testkommentar"]'
  ```

## Nutzungsbedingungen
Die APIs werden unter folgenden [Nutzungsbedingungen](https://docs.api.europace.de/nutzungsbedingungen/) zur Verfügung gestellt
