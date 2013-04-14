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
dass Sie ``schluessel1=wert1``und ``schluessel2=wert2`` an ``httpbin.org/get``
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
benötigen. Wenn Sie Ihre eigene Kodierung erstellt und im Modul ``codecs``registriert
haben, können Sie einfach den Namen der Kodierung als Wert für ``r.encoding`` benutzen
und Requests übernimmt die Dekodierung für Sie.


Binary Response Content
-----------------------

You can also access the response body as bytes, for non-text requests::

    >>> r.content
    b'[{"repository":{"open_issues":0,"url":"https://github.com/...

The ``gzip`` and ``deflate`` transfer-encodings are automatically decoded for you.

For example, to create an image from binary data returned by a request, you can
use the following code:

    >>> from PIL import Image
    >>> from StringIO import StringIO
    >>> i = Image.open(StringIO(r.content))


JSON Response Content
---------------------

There's also a builtin JSON decoder, in case you're dealing with JSON data::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.json()
    [{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...

In case the JSON decoding fails, ``r.json`` raises an exception. For example, if
the response gets a 401 (Unauthorized), attempting ``r.json`` raises ``ValueError:
No JSON object could be decoded``


Raw Response Content
--------------------

In the rare case that you'd like to get the raw socket response from the
server, you can access ``r.raw``. If you want to do this, make sure you set
``stream=True`` in your initial request. Once you do, you can do this::

    >>> r = requests.get('https://github.com/timeline.json', stream=True)
    >>> r.raw
    <requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
    >>> r.raw.read(10)
    '\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'


Custom Headers
--------------

If you'd like to add HTTP headers to a request, simply pass in a ``dict`` to the
``headers`` parameter.

For example, we didn't specify our content-type in the previous example::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}
    >>> headers = {'content-type': 'application/json'}

    >>> r = requests.post(url, data=json.dumps(payload), headers=headers)


More complicated POST requests
------------------------------

Typically, you want to send some form-encoded data — much like an HTML form.
To do this, simply pass a dictionary to the `data` argument. Your
dictionary of data will automatically be form-encoded when the request is made::

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

There are many times that you want to send data that is not form-encoded. If you pass in a ``string`` instead of a ``dict``, that data will be posted directly.

For example, the GitHub API v3 accepts JSON-Encoded POST/PATCH data::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}

    >>> r = requests.post(url, data=json.dumps(payload))


POST a Multipart-Encoded File
-----------------------------

Requests makes it simple to upload Multipart-encoded files::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': open('report.xls', 'rb')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<censored...binary...data>"
      },
      ...
    }

You can set the filename explicitly::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.xls', open('report.xls', 'rb'))}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<censored...binary...data>"
      },
      ...
    }

If you want, you can send strings to be received as files::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "some,data,to,send\\nanother,row,to,send\\n"
      },
      ...
    }


Response Status Codes
---------------------

We can check the response status code::

    >>> r = requests.get('http://httpbin.org/get')
    >>> r.status_code
    200

Requests also comes with a built-in status code lookup object for easy
reference::

    >>> r.status_code == requests.codes.ok
    True

If we made a bad request (non-200 response), we can raise it with
:class:`Response.raise_for_status()`::

    >>> bad_r = requests.get('http://httpbin.org/status/404')
    >>> bad_r.status_code
    404

    >>> bad_r.raise_for_status()
    Traceback (most recent call last):
      File "requests/models.py", line 832, in raise_for_status
        raise http_error
    requests.exceptions.HTTPError: 404 Client Error

But, since our ``status_code`` for ``r`` was ``200``, when we call
``raise_for_status()`` we get::

    >>> r.raise_for_status()
    None

All is well.


Response Headers
----------------

We can view the server's response headers using a Python dictionary::

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

The dictionary is special, though: it's made just for HTTP headers. According to
`RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html>`_, HTTP
Headers are case-insensitive.

So, we can access the headers using any capitalization we want::

    >>> r.headers['Content-Type']
    'application/json; charset=utf-8'

    >>> r.headers.get('content-type')
    'application/json; charset=utf-8'

If a header doesn't exist in the Response, its value defaults to ``None``::

    >>> r.headers['X-Random']
    None


Cookies
-------

If a response contains some Cookies, you can get quick access to them::

    >>> url = 'http://example.com/some/cookie/setting/url'
    >>> r = requests.get(url)

    >>> r.cookies['example_cookie_name']
    'example_cookie_value'

To send your own cookies to the server, you can use the ``cookies``
parameter::

    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_are='working')

    >>> r = requests.get(url, cookies=cookies)
    >>> r.text
    '{"cookies": {"cookies_are": "working"}}'


Redirection and History
-----------------------

Requests will automatically perform location redirection while using the GET
and OPTIONS verbs.

GitHub redirects all HTTP requests to HTTPS. We can use the ``history`` method
of the Response object to track redirection. Let's see what Github does::

    >>> r = requests.get('http://github.com')
    >>> r.url
    'https://github.com/'
    >>> r.status_code
    200
    >>> r.history
    [<Response [301]>]

The :class:`Response.history` list contains a list of the
:class:`Request` objects that were created in order to complete the request. The list is sorted from the oldest to the most recent request.

If you're using GET or OPTIONS, you can disable redirection handling with the
``allow_redirects`` parameter::

    >>> r = requests.get('http://github.com', allow_redirects=False)
    >>> r.status_code
    301
    >>> r.history
    []

If you're using POST, PUT, PATCH, DELETE or HEAD, you can enable
redirection as well::

    >>> r = requests.post('http://github.com', allow_redirects=True)
    >>> r.url
    'https://github.com/'
    >>> r.history
    [<Response [301]>]


Timeouts
--------

You can tell requests to stop waiting for a response after a given number of
seconds with the ``timeout`` parameter::

    >>> requests.get('http://github.com', timeout=0.001)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)


.. admonition:: Note:

    ``timeout`` only effects the connection process itself, not the
    downloading of the response body.


Errors and Exceptions
---------------------

In the event of a network problem (e.g. DNS failure, refused connection, etc),
Requests will raise a :class:`ConnectionError` exception.

In the event of the rare invalid HTTP response, Requests will raise
an  :class:`HTTPError` exception.

If a request times out, a :class:`Timeout` exception is raised.

If a request exceeds the configured number of maximum redirections, a
:class:`TooManyRedirects` exception is raised.

All exceptions that Requests explicitly raises inherit from
:class:`requests.exceptions.RequestException`.

-----------------------

Ready for more? Check out the :ref:`advanced <advanced>` section.
