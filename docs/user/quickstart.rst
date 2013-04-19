.. _quickstart:

Schnellstart
============

.. module:: requests.models

Sie wollen loslegen? Diese Seite bietet eine gute Einführung, wie Sie mit Requests starten.
Wir nehmen an, Sie haben Requests bereits installiert. Falls nicht, finden Sie im Abschnitt
:ref:`Installation <install>` die notwendigen Informationen dazu.

Zuerst stellen Sie bitte sicher, dass:

* Requests :ref:`installiert <install>` ist.
* Requests :ref:`auf dem akutellen Stand <updates>` ist.


Lassen Sie und mit einigen einfachen Beispielen beginnen.

Eine HTTP-Anforderung senden
----------------------------

Es ist sehr einfach, eine Anforderung mit Requests zu senden.

Beginnen Sie mit dem Import des Requests Moduls::

    >>> import requests

Lassen Sie uns nun versuchen, eine Webseite zu laden. Für dieses Beispiel werden wir
die öffnentliche Zeitleiste von Github lesen::

    >>> r = requests.get('https://github.com/timeline.json')

Jetzt haben wir ein Objekt :class:`Response` mit dem Namen ``r``. Wir können alle benötigten 
Informationen von diesem Objekt lesen.

Die einfache API von Requests bedeutet, dass alle Formen von HTTP-Anfragen genau so 
leicht zu erfassen sind. Zum Beispiel führen Sie ein HTTP POST wie folgt durch::

    >>> r = requests.post("http://httpbin.org/post")

Nett, nicht wahr? Wie sieht es mit den anderen HTTP Anforderungen aus: PUT, DELETE, HEAD und
OPTIONS? Die sind alle ebenso einfach::

    >>> r = requests.put("http://httpbin.org/put")
    >>> r = requests.delete("http://httpbin.org/delete")
    >>> r = requests.head("http://httpbin.org/get")
    >>> r = requests.options("http://httpbin.org/get")

Das ist alle schön und gut, stellt aber nur den Anfang von dem dar, was Requests leistet.


Parameter in URLs übergeben
---------------------------

In vielen Fällen werden Sie Daten in irgendeiner Form im Query-String der URL 
senden wollen. Falls Sie die URL per Hand zusammenstellen, würden diese Daten
als Schlüssel/Wert-Paare nach einem Fragezeichen in der URL übergeben werden,
z.B. ``httpbin.org/get?schluessel=wert``.
Requests erlaubt es Ihnen, diese Daten in einem Dictionary zu übergeben, in dem
Sie das ``params`` Schlüsselwortargument benutzen. Als Beispiel nehmen wir an,
dass Sie ``schluessel1=wert1`` und ``schluessel2=wert2`` an ``httpbin.org/get``
übergeben wollen. Dazu benutzen Sie den folgenden Code:: 

    >>> payload = {'schluessel1': 'wert1', 'schluessel2': 'wert2'}
    >>> r = requests.get("http://httpbin.org/get", params=payload)

Wenn Sie die erzeugte URL ausgeben lassen, sehen Sie, dass diese korrekt erzeugt wurde::

    >>> print r.url
    u'http://httpbin.org/get?schluessel2=wert2&schluessel1=wert1'


Daten aus der Antwort
---------------------

Wir können den Inhalt der Antwort des Servers lesen. Betrachten wir noch einmal
die Zeitleiste von Github::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.text
    '[{"repository":{"open_issues":0,"url":"https://github.com/...

Requests dekodiert automatisch den Inhalt der Antwort vom Server. Die meisten
Unicode-Zeichensätze werden problemlos erkannt.

Wenn Sie eine Anfrage absenden, versucht Requests anhand der Header die Kodierung
der Daten in der Antwort zu ermitteln. Die Kodierung, die Requests ermittelt hat,
wird verwendet, wenn Sie auf ``r.text``zugreifen. Sie können herausfinden, welche
Zeichenkodierung Requests verwendet (und diese auch ändern), in dem Sie die
Eigenschaft ``r.encoding``verwenden::

    >>> r.encoding
    'utf-8'
    >>> r.encoding = 'ISO-8859-1'

Falls Sie die Zeichenkodierung ändern, benutzt Requests diese Zuordnung, sobald 
Sie auf ``r.text``zugreifen.

Requests wird auch benutzerdefinierte Zeichenkodierungen benutzen, wenn Sie diese
benötigen. Wenn Sie Ihre eigene Kodierung erstellt und im Modul ``codecs`` registriert
haben, können Sie einfach den Namen der Kodierung als Wert für ``r.encoding`` benutzen
und Requests übernimmt die Dekodierung für Sie.


Binäre Antwortdaten
-------------------

Sie können auch byteweise auf die Serverantwort zugreifen, um nicht-Text Anfragen zu erledigen::

    >>> r.content
    b'[{"repository":{"open_issues":0,"url":"https://github.com/...

Die ``gzip`` und ``deflate`` transfer-encodings werden automatisch für Sie dekodiert.

Um zum Beispiel ein Bild aus den von einer Anfrage gelieferten Binärdaten zu erzeugen,
können Sie den folgenden Code benutzen::

    >>> from PIL import Image
    >>> from StringIO import StringIO
    >>> i = Image.open(StringIO(r.content))


JSON-basierte Antwortdaten
--------------------------

Es gibt auch einen eingebauten JSON Decoder, falls Sie mit JSON Daten arbeiten::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.json()
    [{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...

Sollte die Dekodierung der JSON-Daten fehlschlagen, erzeugt ``r.json`` eine Exception.
Erhalten Sie beispielsweise einen HTTP-Statuscode 401 (Unauthorized) zurück, wird ein
Zugriff auf ``r.json`` einen  ``ValueError: No JSON object could be decoded`` auslösen.


Rohdaten der Antwort
--------------------

Für den seltenen Fall, dass Sie auf die unverarbeiteten Daten des raw sockets
der Antwort zugreifen wollen, können Sie ``r.raw`` bneutzen. Falls Sie das wollen,
stellen Sie sicher, dass Sie ``stream=True`` in Ihrer Anfrage setzen. Nachdem das
erledigt ist, können Sie folgenden Code benutzen::

    >>> r = requests.get('https://github.com/timeline.json', stream=True)
    >>> r.raw
    <requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
    >>> r.raw.read(10)
    '\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'


Benutzerdefinierte Header
-------------------------

Wenn Sie bestimmte Header zu der HTTP-Anfrage hinzufügen wollen, übergeben Sie einfach
ein Dictionary über den ``headers`` Parameter.

Wir haben zum Beispiel im letzten Code-Beispiel nicht den gewünschten Content-Type angegeben::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}
    >>> headers = {'content-type': 'application/json'}

    >>> r = requests.post(url, data=json.dumps(payload), headers=headers)


Komplexere POST Anfragen
------------------------

Typischerweise möchten Sie einige formular-kodierte Daten senden - so wie in einem HTML-Formular.
Um dies zu erledigen, übergeben Sie einfach ein Dictionary an das `data` Argument. Dieses
Dictionary mit den Daten wird automatisch formular-kodiert, wenn die Anfrage ausgeführt wird::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.post("http://httpbin.org/post", data=payload)
    >>> print r.text
    {
      ...
      "form": {
        "key2": "value2",
        "key1": "value1"
      },
      ...
    }


In vielen Fällen werden Sie auch Daten senden wollen, die nicht formular-kodiert sind.
Wenn Sie an Stelle eines Dictionary einen ``string`` übergeben, werden diese Daten direkt übertragen.

So akzeptiert die GitHub API v3 beispielsweise JSON-kodierte Strings für POST/PATCH-Daten::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}

    >>> r = requests.post(url, data=json.dumps(payload))


POST mit Multipart-Dateien
--------------------------

Requests macht das Hochladen von Dateien in Multipart-Kodierung einfach::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': open('report.xls', 'rb')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<ganz...geheime...binaere...daten>"
      },
      ...
    }

Sie können den Dateinamen explizit angeben::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.xls', open('report.xls', 'rb'))}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<ganz...geheime...binaere...daten>"
      },
      ...
    }

Falls Sie das wollen, können Sie Strings als Dateien hochladen::

    >>> url = 'http://httpbin.org/post'
    .. >>> files = {'file': ('report.csv', 'einige,daten,zum,senden\\nnoch,eine,zweite,zeile\\n')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "einige,daten,zum,senden\\nnoch,eine,zweite,zeile\\n"
      },
      ...
    }


Status Codes der Antwort
------------------------

Wir können den Statuscode der Antwort prüfen::

    >>> r = requests.get('http://httpbin.org/get')
    >>> r.status_code
    200

Requests hat auch ein eingebautes Objekt zum Nachschlagen von Statuscodes::

    >>> r.status_code == requests.codes.ok
    True

Falls wir eine ungültige Anfrage (eine Antwort mit einem Status ungleich 200) ausgeführt
haben, können wir über :class:`Response.raise_for_status()` eine Exception werfen::

    >>> bad_r = requests.get('http://httpbin.org/status/404')
    >>> bad_r.status_code
    404

    >>> bad_r.raise_for_status()
    Traceback (most recent call last):
      File "requests/models.py", line 832, in raise_for_status
        raise http_error
    requests.exceptions.HTTPError: 404 Client Error

Aber, da unser ``status_code`` für ``r`` ein ``200`` war, erhalten wir beim Aufruf
von ``raise_for_status()`` ::

    >>> r.raise_for_status()
    None

Alles ist gut.


Header der Antwort
------------------

Wir können die Header der Serverantwort als Python Dictionary lesen::

    >>> r.headers
    {
        'content-encoding': 'gzip',
        'transfer-encoding': 'chunked',
        'connection': 'close',
        'server': 'nginx/1.0.4',
        'x-runtime': '148ms',
        'etag': '"e1ca502697e5c9317743dc078f67693f"',
        'content-type': 'application/json; charset=utf-8'
    }

Dieses Dictionary ist aber ein spezielles: es ist nur für HTTP-Header gemacht.
Nach dem `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html>`_ sind
HTTP Header nicht abhängig von Groß- oder Kleinschreibung.

Daher können wir in beliebiger Schreibweise auf die Header zugreifen::

    >>> r.headers['Content-Type']
    'application/json; charset=utf-8'

    >>> r.headers.get('content-type')
    'application/json; charset=utf-8'

Falls ein Header nicht in der Antwort enthalten ist, wird als Wert der Default von ``None`` geliefert::

    >>> r.headers['X-Random']
    None


Cookies
-------

Falls eine Antwort ein oder mehrere Cookies enthält, können Sie einfach und schnell darauf zugreifen::

    >>> url = 'http://example.com/some/cookie/setting/url'
    >>> r = requests.get(url)

    >>> r.cookies['beispiel_cookie_name']
    'beispiel_cookie_wert'

Um eigene Cookies an den Server zu senden, können Sie den ``cookies`` Parameter benutzen::

    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_sind='keksig')

    >>> r = requests.get(url, cookies=cookies)
    >>> r.text
    '{"cookies": {"cookies_sind": "keksig"}}'


Redirections und Verlauf
------------------------

Requets führt automatisch Weiterleitungen aus, wenn eines der beiden Verben GET oder OPTIONS benutzt wird.

GitHub zum Beispiel leitet alle HTTP-Anfragen auf HTTPS um. Wir können die ``history`` Methode
des Antwortobjekts benutzen, um diese Weiterleitungen zu verfolgen. Sehen wir und an, was GitHub macht::

    >>> r = requests.get('http://github.com')
    >>> r.url
    'https://github.com/'
    >>> r.status_code
    200
    >>> r.history
    [<Response [301]>]

Die :class:`Response.history` Liste enthält eine Liste der 
:class:`Request` Objekte, die erzeugt wurden, um die Anfrage abzuschließen. Diese Liste ist vom ältesten
zum neuesten Objekt sortiert.

Falls Sie GET oder OPTIONS benutzen, können Sie die Handhabung von Weiterleitungen mit Hilfe des
``allow_redirects`` Parameters kontrollieren::

    >>> r = requests.get('http://github.com', allow_redirects=False)
    >>> r.status_code
    301
    >>> r.history
    []

Falls Sie POST, PUT, PATCH, DELETE oder HEAD benutzen, können Sie bei Bedarf damit ebenso die 
Behandlung von Weiterleitungen aktivieren::

    >>> r = requests.post('http://github.com', allow_redirects=True)
    >>> r.url
    'https://github.com/'
    >>> r.history
    [<Response [301]>]


Timeouts
--------

Sie können Anfragen nach einer bestimmten Anzahl von Sekunden anweisen, nicht länger zu warten,
in dem Sie den ``timeout`` Parameter benutzen::

    >>> requests.get('http://github.com', timeout=0.001)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)


.. admonition:: Hinweis:

    ``timeout`` betrifft nur den Prozess des Verbindungsaufbaus, nicht das Herunterladen der Antwort selbst.


Fehler und Exceptions
---------------------

Im Falle eines Netzwerkproblems (z.B. DNS-Fehler, abgewiesene Verbindung, etc.) löst Requests einen
:class:`ConnectionError` Ausnahmefehler aus.

Im Fall einer (seltenen) ungültigen HTTP Antwort löst Requests einen 
:class:`HTTPError` Ausnahmefehler aus.

Falls eine Anfrage eine Zeitüberschreitung auslöst, wird ein
:class:`Timeout` Ausnahmefehler ausgelöst.

Falls eine Anfrage die konfigurierte maximale Anzahl von Weiterleitungen überschreitet,
wird ein :class:`TooManyRedirects` Ausnahmefehler ausgelöst.

Alle Ausnahmefehler, die Requests explizit auslöst, erben von 
:class:`requests.exceptions.RequestException`.

-----------------------

Bereit für mehr? Dann sehen Sie sich den Abschnitt :ref:`Weitergehende Informationen <advanced>` an.
