# KEX-Vorgang-Update-API 


## Inhaltsverzeichnis

* [Allgemeines](#allgemeines)
  * [Authentifizierung](#authentifizierung)
  * [Nachverfolgbarkeit von Requests](#traceid-zur-nachverfolgbarkeit-von-requests)
  * [Content-Type](#content-type)
  * [Fehlercodes](#fehlercodes)   
* [Dokumente](#dokumente)
  * [Request Format](#request-format)
  * [Beispiel](#post-request-beispiel)
* [Kommentare](#kommentare)
  * [Request Format](#request-format-1)
  * [Beispiel](#post-request-beispiel-1)

# Allgemeines

Dies ist eine Sammlung von verschiedenen Schnittstellen, die es ermöglichen Daten zu einem __existierenden__ Vorgang hinzuzufügen oder zu ändern.

## Authentifizierung

Für jeden Request ist eine Authentifizierung erforderlich.

Die Authentifizierung erfolgt über einen HTTP Header.

| Request Header Name | Beschreibung                       |
|:--------------------|:-----------------------------------|
| X-Authentication    | API JWT der Vertriebsorganisation  |


Das API JWT (JSON Web Token) erhalten Sie von Ihrem Ansprechpartner im **Kredit**Smart-Team. 

Schlägt die Authentifizierung fehl, erhält der Aufrufer eine HTTP Response mit Statuscode **401 UNAUTHORIZED**.

## TraceId zur Nachverfolgbarkeit von Requests

Für jeden Request soll eine eindeutige ID generiert werden, die den Request im EUROPACE 2 System nachverfolgbar macht und so bei etwaigen Problemen oder Fehlern die systemübergreifende Analyse erleichtert.

Die Übermittlung der X-TraceId erfolgt über einen HTTP Header. Dieser Header ist optional,
wenn er nicht gesetzt ist, wird eine ID vom System generiert.  

| Request Header Name | Beschreibung                     | Beispiel   |
|:--------------------|:---------------------------------|:-----------|
| X-TraceId           | eindeutige Id für jeden Request  |sys12345678 |

## Content-Type

Die Schnittstelle akzeptiert Daten mit Content-Type "**application/json**".

Entsprechend muss im Request der Content-Type Header gesetzt werden und zusätzlich das Encoding, wenn es nicht UTF-8 ist.

| Request Header Name | Header Value       |
|:--------------------|:-------------------|
| Content-Type         | application/json |

## Fehlercodes

Wenn der Request nicht erfolgreich verarbeitet werden konnte, liefert die Schnittstelle einen Fehlercode, auf den die aufrufende Anwendung reagieren kann, zurück.

| Fehlercode | Nachricht    | Erklärung                                                                   |
|:-----------|:-------------|:----------------------------------------------------------------------------|
| 400        | Bad Request  | Die Daten entsprechen nicht dem erwarteten Format oder Pflichtfelder fehlen |
| 401        | Unauthorized | Authentifizierung ist fehlgeschlagen                                        |
| 403        | Forbidden    | Autorisierung ist fehlgeschlagen                                            |
| 410        | Gone         | Vorgang ist gelöscht                                                        |

Weitere Fehlercodes und ihre Bedeutung siehe Wikipedia: [HTTP-Statuscode](https://de.wikipedia.org/wiki/HTTP-Statuscode)


# Dokumente

Die Schnittstelle ermöglicht das automatisierte Importieren von Dokumenten in 
einen existierenden **Kredit**Smart-Vorgang.

Dokumente können per **HTTP POST** importiert werden.

Die URL ist:

    https://www.europace2.de/kreditsmart/kex/vorgang/{vorgangsnummer}/dokument
    
Die Daten werden als JSON Dokument im Body des POST Requests übermittelt.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **201 CREATED**.

## Request Format

Die Angaben werden als JSON im Body des Requests gesendet.

	{
		"filename": String,
		"base64Content": String
	}

Beide Felder müssen befüllt sein um das Dokument erfolgreich importieren zu können.

### POST Request Beispiel:

	POST https://www.europace2.de/kreditsmart/kex/vorgang/123456/dokument
	X-Authentication: xxxxxxx
	Content-Type: application/json;charset=utf-8
	{
		"filename": "Test.pdf",
		"base64Content": "JVBERi0xLjMKJcTl8uXrp"
	}


# Kommentare

Die Schnittstelle ermöglicht das automatisierte Importieren von Kommentaren in 
einen existierenden **Kredit**Smart-Vorgang.

Kommentare können per **HTTP POST** importiert werden.

Die URL ist:

    https://www.europace2.de/kreditsmart/kex/vorgang/{vorgangsnummer}/kommentare
    
Die Daten werden als JSON Dokument im Body des POST Requests übermittelt.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **200 OK**.

## Request Format

Die Kommentare werden als JSON im Body des Requests gesendet. 
Es kann eine Liste von Strings übermittelt werden. Jedes Element der Liste erzeugt einen neuen Kommentar.

	[ <Kommentar> ]

Beide Felder müssen befüllt sein um das Dokument erfolgreich importieren zu können.

### POST Request Beispiel:

	POST https://www.europace2.de/kreditsmart/kex/vorgang/123456/dokument
	X-Authentication: xxxxxxx
	Content-Type: application/json;charset=utf-8
	[ "Testkommentar" ]
