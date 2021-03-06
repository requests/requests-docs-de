.. _advanced:

Fortgeschrittene Nutzung
========================

Dieses Dokument behandelt einige der fortgeschrittenen Features von Requests.


Sesssion-Objekte
----------------

Das Session-Objekt erlaubt das Persistieren einiger Parameter über einzelne Anfragen hinweg.
Es persistiert auch Cookies für die Dauer aller Anfragen einer Session-Instanz.

Ein Session-Objekt besitzt alle Methoden der Haupt-API von Requests (get,put, post, delete, head, options).

Lassen Sie und mit Cookies über mehrere Anfragen hinweg arbeiten::

    s = requests.Session()

    s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get("http://httpbin.org/cookies")

    print r.text
    # '{"cookies": {"sessioncookie": "123456789"}}'


Sessions können auch dazu benutzt werden, um für die Methoden der Anfrage Standardwerte vorzuhalten.
Dies geschieht durch die Angaben von Eigenschaften eines Session-Objekts::

    s = requests.Session()
    s.auth = ('user', 'pass')
    s.headers.update({'x-test': 'true'})

    # sowohl 'x-test' als auch 'x-test2' werden als Header übermittelt
    s.get('http://httpbin.org/headers', headers={'x-test2': 'true'})

Alle Dictionaries, die Sie einer Anfragemethode übergeben, werden mit den Werten 
der Session zusammengeführt, bevor sie gesendet werden. 
Falls an beiden Stellen Angaben gemacht werden, überschreiben die Parameter auf
Methodenebene die des Session-Objekts.

.. admonition:: Einen Wert aus einem Dictionary-Parameter entfernen

    Unter Umständen möchten Sie einzelne Werte aus den Session-Daten nicht übermitteln.
    Dies können Sie dadurch erreichen, dass Sie den Schlüssel im Dictionary beim Aufruf der 
    Methode angeben, aber als Wert ``None``angeben. Dieser Schlüssel wird dann automatisch
    ausgelassen.

Alle Werte, die innerhalb einer Session verwendet werden, stehen Ihnen direkt zur Verfügug.
Sehen Sie dazu unter :ref:`Session API  <sessionapi>` nach, um mehr zu erfahren.

Objekte für Anfrage und Antwort
-------------------------------

Wann immer ein Aufruf von requests.*() erfolgt, passieren zwei Dinge.
Zuerst erzeugen Sie damit ein ``Request`` Objekt, dass an einen Server
geschickt wird, um eine Anfrage auszuführen oder eine Ressource zu lesen.
Zweitens wird, sobald Requests eine Antwort vom Server erhält, ein
``Response`` Objekt erzeugt. Dieses ``Response`` Objekt enthält alle vom
Server gelieferten Informationen sowie das ursprünglich erzeugte ``Request`` Objekt.
Hier ist eine einfaxche Anfrage, um einige sehr wichtige Daten aus der Wikipedia
zu lesen::

    >>> r = requests.get('http://en.wikipedia.org/wiki/Monty_Python')

Wenn Sie wissen wollen, welche Header der Server an uns zurück geschickt hat,
tun Sie folgendes::

    >>> r.headers
    {'content-length': '56170', 'x-content-type-options': 'nosniff', 'x-cache':
    'HIT from cp1006.eqiad.wmnet, MISS from cp1010.eqiad.wmnet', 'content-encoding':
    'gzip', 'age': '3080', 'content-language': 'en', 'vary': 'Accept-Encoding,Cookie',
    'server': 'Apache', 'last-modified': 'Wed, 13 Jun 2012 01:33:50 GMT',
    'connection': 'close', 'cache-control': 'private, s-maxage=0, max-age=0,
    must-revalidate', 'date': 'Thu, 14 Jun 2012 12:59:39 GMT', 'content-type':
    'text/html; charset=UTF-8', 'x-cache-lookup': 'HIT from cp1006.eqiad.wmnet:3128,
    MISS from cp1010.eqiad.wmnet:80'}

Wollen Sie dagegen wissen, welche Header wir ursprünglich an den Server gesendet haben,
greifen wir eindach auf die Anfrage und darin auf die Header von ``request`` zu::

    >>> r.request.headers
    {'Accept-Encoding': 'identity, deflate, compress, gzip',
    'Accept': '*/*', 'User-Agent': 'python-requests/1.2.0'}


Prepared Requests
-----------------

Wann immer SIe ein :class:`Response <requests.models.Response>` Objekt von einem
API-Aufruf oder einem Session-Aufruf zurück erhalten, handelt es sich bei der
``request`` Eigenschaft tatsächlich um die ``PreparedRequest`` Eigenschaft, die
benutzt wurde. In einigen Fällen möchten Sie vielleicht zusätzliche Bearbeitungen
am Body oder den Headern (oder etwas anderem) vornehmen, bevor Sie die Anfrage absenden.
Der einfache Weg dazu ist folgender::

    from requests import Request, Session

    s = Session()
    prepped = Request('GET',  # oder irgendeine andere Methode, 'POST', 'PUT', etc.
                      url,
                      data=data
                      headers=headers
                      # ...
                      ).prepare()
    # führen Sie mit prepped.body eine Aktion durch
    # oder
    # führen Sie eine Aktion mit prepped.headers durch
    resp = s.send(prepped,
                  stream=stream,
                  verify=verify,
                  proxies=proxies,
                  cert=cert,
                  timeout=timeout,
                  # etc.
                  )
    print(resp.status_code)

Nachdem Sie nichts Besonderes mit dem ``Request`` Objekt gemacht haben, bereiten
Sie das sofort durch den Aufruf von prepare() vor und veändern das ``PreparedRequest`` Objekt.
Dieses senden Sie dann mit den anderen Parametern, die Sie auch an ``requests.*`` oder an
``Session.*`` gesendet hätten.


Überprüfen von SSL-Zertifikaten
-------------------------------

Requests kann SSL-Zertifikate für HTTPS-Anfragen überprüfen, genau wie ein Web Browser. Um 
das SSL Zertifikat eines Hosts zu überprüfen, können Sie den ``verify`` Parameter benutzen::

    >>> requests.get('https://kennethreitz.com', verify=True)
    requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'

Ich habe SSL für diese Domain nicht aktiviert, deshalb schlägt die Überprüfung fehl. Exzellent.
GitHub dagegen hat SSL aktiviert::

    >>> requests.get('https://github.com', verify=True)
    <Response [200]>

Sie können auch anstelle von ``True`` den Pfad zu einer CA_BUNDLE Datei für private Zertifikate
übergeben. Ebenso können Sie die ``REQUESTS_CA_BUNDLE`` Umgebungsvariable setzen.

Requests kann auch die Überprüfung des SSL Zertifikates ignorieren, wenn Sie ``verify`` auf ``False`` setzen.

::

    >>> requests.get('https://kennethreitz.com', verify=False)
    <Response [200]>

Als Standard steht ``verfiy`` auf ``True`` und kann nur für Hostzertifikate verwendet werden.

You can also specify a local cert to use as client side certificate, as a single file (containing the private key and the certificate) or as a tuple of both file's path::

    >>> requests.get('https://kennethreitz.com', cert=('/path/server.crt', '/path/key'))
    <Response [200]>

Falls Sie einen ungültigen Pfad oder ein ungültiges Zertifikat angeben::

    >>> requests.get('https://kennethreitz.com', cert='/wrong_path/server.pem')
    SSLError: [Errno 336265225] _ssl.c:347: error:140B0009:SSL routines:SSL_CTX_use_PrivateKey_file:PEM lib


Workflow für Antwortdaten
-------------------------

Standardmäßig wird, wenn Sie eine Anfrage ausführen, der Inhalt (body) der Antwort sofort herunter
geladen. Sie können dieses Verhalten überschreiben und das Herunterladen der Antwort verzögern,
bis Sie auf die :class:`Response.content` Eigenschaft zugreifen. Benutzen Sie dazu den
``stream`` Parameter::

    tarball_url = 'https://github.com/kennethreitz/requests/tarball/master'
    r = requests.get(tarball_url, stream=True)

Zu diesem Zeitpunkt wurden nur die Header der Antwort herunter geladen und die Verbindung
bleibt offen. Damit wird und erlaubt, das Lesen des Antwortsinhalts optional zu gestalten::

    if int(r.headers['content-length']) < TOO_LONG:
      content = r.content
      ...

Sie können den Ablauf durch die Methoden :class:`Response.iter_content` und :class:`Response.iter_lines` 
weiter kontrollieren oder indem Sie von der darunter liegenden urllib3-Klasse :class:`urllib3.HTTPResponse` 
die Eigenschaft :class:`Response.raw` lesen.


Keep-Alive
----------

Gute Nachrichten - dank der urllib3 funktioniert keep-alive 100% automatisch innerhalb einer Session.
Jede Anfrage, die Sie innerhalb einer Session ausführen, wird automatisch die jeweilige Verbindung wieder verwenden!


Bitte beachten Sie, dass Verbindungen nur dann an den Pool zurück gegeben werden, 
nachdem alle Daten der Antwort gelesen wurden. Stellen Sie sicher, dass entweder 
``stream`` auf ``False`` gesetzt wurde oder Sie die ``content`` Eigenschaft des
``Response`` Objekts gelesen haben.



Streaming Uploads
-----------------

Requests unterstützt Streaming bei Uploads, damit Sie in der Lage sind, große Streams oder Dateien 
zu senden, ohne diese erst in den Speicher laden zu müssen. Um den Upload zu streamen, geben Sie
für die Nutzdaten einfach ein Dateiobjekt an::

    with open('riesen-body') as f:
        requests.post('http://some.url/streamed', data=f)


Paketierte Anforderungen (*chunked encoding*)
---------------------------------------------

Requests unterstützt auch die paketierte Kodierung für die Übertragung (sog. *chunked encoding*)
für ausgehende und ankommende Anfragen. Um eine Anfrage in Datenpakete zerteilt zu übertragen,
geben Sie einfach einen Generator (oder einen Iterator ohne Länge) für die Daten an::


    def gen():
        yield 'hi'
        yield 'there'

    requests.post('http://some.url/chunked', data=gen())


Event Hooks
-----------

Reqeusts hat ein System für Event Hooks, das Sie benutzen können, um sich in
den Anfrageprozess einzuhängen oder Ereignisse zu signalisieren.

Verfügbare Hooks:

``response``:
    Die Antwort auf eine Anforderung.

Sie können eine Funktion für einen Hook auf Anfragebasis zuweisen, in dem Sie
ein ``{hook_name: callback_function}`` Dictionary an den ``hooks`` Parameter
übergeben::

    hooks=dict(response=print_url)

Diese ``callback_function`` erhält ein Datenpaket als erstes Argument.

::

    def print_url(r):
        print(r.url)

Falls während der Ausführung des Callbacks ein Fehler passiert, erhalten Sie eine Warnung.

Wenn die Callback-Funktion einen Wert zurück liefert, wird angenommen, dass dieser die
Datenersetzen soll, die übergeben wurden. Wenn die Funktion nichts zurück liefert, wird
auch nichts verändert.

Lassen Sie uns einige Argumente der Anfrage-Methode zur Laufzeit ausgeben::

    >>> requests.get('http://httpbin.org', hooks=dict(response=print_url))
    http://httpbin.org
    <Response [200]>


Benutzerdefinierte Authentifizierung
------------------------------------

Reqeusts erlaubt es Ihnen, Ihren eigenen Authentifizierungsmechanismus anzugeben.

Jedes Callable, das als das ``auth`` Argument einer Anfrage übergeben wird,
hat die Möglichkeit, die Anfrage vor der Weiterleitung zu modifizieren.

Implementierungen für eine Authentifizierung sind Unterklassen von ``requests.auth.AuthBase``
und einfach zu definieren. Reqeusts bietet zwei Implementierungen üblicher Authentifizierungs-Schemata
in ``requests.auth``: ``HTTPBasicAuth`` and ``HTTPDigestAuth``.

Nehmen wir an, dass wir einen Webservice haben, der nur dann reagiert, wenn der ``X-Pizza`` Header
auf einen Wert mit einer Kennung gesetzt wurde. Unwahrscheinlich, aber für das Beispiel nehmen 
wir das einfach mal an.

::

    from requests.auth import AuthBase

    class PizzaAuth(AuthBase):
        """Bindet HTTP Pizza Authentifizierung an das angegebene Anfrageobjekt."""
        def __init__(self, username):
            # hier erfolgt die vorbereitung für auth-relevante daten
            self.username = username

        def __call__(self, r):
            # wir verändern die anfrage und liefern diese zurück
            r.headers['X-Pizza'] = self.username
            return r

Jetzt können wir unsere Anfrage mit der Pizza-Authentifizierung durchführen::

    >>> requests.get('http://pizzabin.org/admin', auth=PizzaAuth('kenneth'))
    <Response [200]>


Streaming mit Requests
----------------------

Mit ``requests.Response.iter_lines()`` können Sie einfach über Streaming APIs wie z.B.
die `Twitter Streaming API <https://dev.twitter.com/docs/streaming-api>`_ iterieren.

Sagen wir der Twitter Streaming API, dass wir das Schlüsselwort "requests" verfolgen wollen::


    import requests
    import json

    r = requests.post('https://stream.twitter.com/1/statuses/filter.json',
        data={'track': 'requests'}, auth=('username', 'password'), stream=True)

    for line in r.iter_lines():
        if line: # keep-alive zeilen ausfiltern
            print json.loads(line)


Proxies
-------

Falls Sie einen Proxy benutzen müssen, können Sie individuelle Anfragen mit dem ``proxies`` 
Argument ausstatten::

    import requests

    proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "http://10.10.1.10:1080",
    }

    requests.get("http://example.org", proxies=proxies)

Sie können Proxies auch über die Umgebungsvariablen ``HTTP_PROXY`` und ``HTTPS_PROXY`` konfigurieren.

::

    $ export HTTP_PROXY="http://10.10.1.10:3128"
    $ export HTTPS_PROXY="http://10.10.1.10:1080"
    $ python
    >>> import requests
    >>> requests.get("http://example.org")

Um HTTP Basic Auth mit ihrem Proxy zu benutzen, verwenden Sie die übliche 
`http://user:password@host/` Syntax::

    proxies = {
        "http": "http://user:pass@10.10.1.10:3128/",
    }


Standardkonformität
-------------------

Es ist die Absicht des Requests-Projektes, alle relevanten Spezifikationen und
RFCs zu beachten, wo diese Konformität keine Schwierigkeiten für die Benutzer bedeutet.
Dieser Fokus auf die Beachtung der Standards kann zu verhalten führen, dass für
nicht mit diesen Standards und RFCs vertrauten Benutzern auf den ersten Blick
ungewöhnlich aussieht.


Zeichenkodierung
^^^^^^^^^^^^^^^^

Wenn Sie eine Antwort vom Server erhalten, versucht Requests das für die
Dekodierung der Antwortdaten nötige Encoding zu erschließen, wenn Sie die
``Response.text`` Methode aufrufen. Requests prüft zuerst, ob in den HTTP Headern
eine Zeichenkodierung angegeben ist. Falls kein Encoding im Header vorhanden ist,
benutzt Requests `charade <http://pypi.python.org/pypi/charade>`_ für einen
Versuch, das Encoding zu erschließen.

Der einzige Fall, bei dem Requests nicht versuchen wird, die Zeichenkodierung
zu erraten, ist der, dass keine explizite Angabe des Encodings im Header vorhanden
ist **und** dass der ``Content-Type`` Header den Inhalt ``text`` besitzt. In diesem
Fall gibt der `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.7.1>`_
an, dass der Standardzeichensatz ``ISO-8859-1`` sein muss. Requests folgt in diesem
Fall der RFC-Spezifikation. Falls Sie ein anderes Encoding benötigen, können Sie
manuell die Eigenschaft ``Response.encoding`` setzen. Alternativ können Sie auch
den unverarbeiteten (raw) ``Response.content`` benutzen.


HTTP Verben
-----------

Requests erlaubt den Zugriff auf fast alle HTTP Verben: GET, OPTIONS, HEAD,
POST, PUT, PATCH und DELETE. Der folgende Abschnitt bietet Ihnen detaillierte
Beispiele, wie diese Verben in Requests benutzt werden. Als Beispiel dient
uns die GitHub API.

Wir beginnen mit dem am meisten verwendeten Verb: GET. HTTP GET ist eine
idempotente Methode, die eine Ressource von einer angegebenen URL liest.
Daher sollten Sie dieses Verb benutzen, wenn Sie Daten von einer URL im 
Web lesen wollen. Ein Beispiel wäre der Versuch, Informationen über einen
bestimmten Commit in GitHub zu erhalten. Nehmen wir an, wir wollten den
Commit `à050faf`` aus dem Requests-Repository liesen. Dies erreichen
Sie mit dem folgenden Code::

    >>> import requests
    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/git/commits/a050faf084662f3a352dd1a941f2c7c9f886d4ad')

Wir sollten sicherstellen, dass GitHub korrekt geantwortet hat. Falls dies
der Fall ist, wollen wir herausfinden, wie der Inhalt der Antwort aussieht.
Dies geht so::


    >>> if (r.status_code == requests.codes.ok):
    ...     print r.headers['content-type']
    ...
    application/json; charset=utf-8

GitHub liefert also JSON. Das ist großartig, denn wir können die ``r.json`` Methode
benutzen, um die Antwort in Python-Objekte zu zerlegen.

::

    >>> commit_data = r.json()
    >>> print commit_data.keys()
    [u'committer', u'author', u'url', u'tree', u'sha', u'parents', u'message']
    >>> print commit_data[u'committer']
    {u'date': u'2012-05-10T11:10:50-07:00', u'email': u'me@kennethreitz.com', u'name': u'Kenneth Reitz'}
    >>> print commit_data[u'message']
    makin' history

So weit, so einfach. Lassen Sie uns die GitHub API etwas genauer betrachten.
Wir könnten dazu die Dokumentation lesen, aber es macht mehr Spaß, wenn wir 
das stattdessen mit Requests tun. Wir können dazu das OPTIONS Verb benutzen,
um zu sehen, welche HTTP Methoden von der gerade benutzten URL unterstützt werden.

::

    >>> verbs = requests.options(r.url)
    >>> verbs.status_code
    500

He! Was? Das ist nicht hilfreich! Wie sich heraus stellt, implementiert GitHub,
wie viele andere Dienste, die im WEB eine API anbieten, die OPTIONS-Methode nicht wirklich.
Das ist etwas ärgerlich, denm jetzt müssen wir doch die langweilige Dokumentation lesen.
Würde GitHub OPTIONS korrekt implementiert haben, dann könnte man mit diesem Verb
alle erlaubten HTTP Methoden für die URL zurück erhalten, wie zum Beispiel so::

    >>> verbs = requests.options('http://a-good-website.com/api/cats')
    >>> print verbs.headers['allow']
    GET,HEAD,POST,OPTIONS

Nachdem wir die Dokumentation gelesen haben, sehen wir, dass die einzige andere
Methode, die für Commits erlaubt wird, ein POST ist. Dies erzeugt einen neuen Commit.
Da wir für unsere Beispiele bisher das Requests-Repository benutzt haben, wäre es
vielleicht besser, nicht einfach wilde Änderungen per POST abzusenden. Lassen Sie
und daher etwas mit dem Ticket-Feature von GitHub spielen.

Diese Dokumentation wurde als Antwort auf Ticket #482 hinzugefügt. Da wir deshalb
davon ausgehen können, dass es dieses Ticket gibt, werden wir es in den Beispielen
benutzen. Starten wir damit, dass wir das Ticket lesen.

::

    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/issues/482')
    >>> r.status_code
    200
    >>> issue = json.loads(r.text)
    >>> print issue[u'title']
    Feature any http verb in docs
    >>> print issue[u'comments']
    3

Cool, wir haben drei Kommentare zu Ticket #482. Sehen wir und den letzten an.

::

    >>> r = requests.get(r.url + u'/comments')
    >>> r.status_code
    200
    >>> comments = r.json()
    >>> print comments[0].keys()
    [u'body', u'url', u'created_at', u'updated_at', u'user', u'id']
    >>> print comments[2][u'body']
    Probably in the "advanced" section

Der Abschnitt "advanced"? Na dann schreiben wir doch einen Kommentar,
dass wir uns gleich auf diese Aufgabe stürzen. Aber wer ist der Kerl überhaupt? ;-)

::

    >>> print comments[2][u'user'][u'login']
    kennethreitz

OK, sagen wir diesem Kenneth, dass wir los legen. Nach der Dokumentation der GitHub API können wir dies tun,
indem wir mit einem POST einen Kommentar hinzufügen. Dann machen wir das auch.

::

    >>> body = json.dumps({u"body": u"Klingt großartig! Ich mach mich gleich daran!"})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/482/comments"
    >>> r = requests.post(url=url, data=body)
    >>> r.status_code
    404

Oha, das ist merkwürdig! Wahrscheinlich müssen wir und anmelden. Das wir bestimmt schwierig, nicht?
Falsch. Requests macht es uns sehr einfach, verschiedene Formen der Authentifizierung zu
benutzen, unter anderem die sehr verbreitete *Basic Authentication*.

::

    >>> from requests.auth import HTTPBasicAuth
    >>> auth = HTTPBasicAuth('fake@example.com', 'hier_wurde_das_passwort_stehen')
    >>> r = requests.post(url=url, data=body, auth=auth)
    >>> r.status_code
    201
    >>> content = r.json()
    >>> print content[u'body']
    Sounds great! I'll get right on it.

Perfekt. Oh nein, doch nicht! Bevor wir die Dokumentation schreiben, muss zuerst die Katze
gefüttert werden, das dauert eine Weile. Könnten wir doch nur den Kommentar bearbeiten!
Glücklicherweise erlaubt uns die GitHub API ein anderes HTTP verb zu benutzen: PATCH.
Verwenden wir also PATCH, um den Kommentar zu ändern.

::

    >>> print content[u"id"]
    5804413
    >>> body = json.dumps({u"body": u"Klingt gut! Ich mache mich dran, sobald ich die Katze gefüttert habe!"})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/comments/5804413"
    >>> r = requests.patch(url=url, data=body, auth=auth)
    >>> r.status_code
    200

Exzellent. Jetzt quälen wir diesen Kenneth etwas und bringen ihn ins Schwitzen, weil wir 
ihm nicht sagen, dass wir an der Dokumentation arbeiten. Dazu löschen wir diesen Kommentar wieder.
GitHub lässt und Kommentare durch das sehr passend benannte Verb DELETE löschen. Gut, werden
wir den Kommentar los ...

::

    >>> r = requests.delete(url=url, auth=auth)
    >>> r.status_code
    204
    >>> r.headers['status']
    '204 No Content'

Nochmal exzellent. Alles weg. Alles, was ich jetzt noch möchte, ist eine Auskunft, 
wie viel von meiner `Änderungsrate <http://developer.github.com/v3/#rate-limiting>`_ ich verbraucht habe. 
Lassen Sie uns das heraus finden. GitHub sendet diese Informationen in einem header, daher werde
ich, anstatt die komplette Seite zu laden, nur über das Verb HEAD die Header lesen.

::

    >>> r = requests.head(url=url, auth=auth)
    >>> print r.headers
    ...
    'x-ratelimit-remaining': '4995'
    'x-ratelimit-limit': '5000'
    ...

Sehr gut. Zeit, noch mehr Python Code zu schreiben, der die GitHub APU auf alle erdenklichen
Arten missbraucht; und das noch 4995 mal in der nächsten Stunde.


Link Header
-----------

Viele HTTP APIs benutzen sogenannte Links Header. Diese machen APIs leichter verstehbar und auch
leichter zu erforschen.

GitHub beispielsweise benutzt solche Link Header für die `Pagination <http://developer.github.com/v3/#pagination>`_, 
(das seitenweise Blättern) in seiner API::

    >>> url = 'https://api.github.com/users/kennethreitz/repos?page=1&per_page=10'
    >>> r = requests.head(url=url)
    >>> r.headers['link']
    '<https://api.github.com/users/kennethreitz/repos?page=2&per_page=10>; rel="next", <https://api.github.com/users/kennethreitz/repos?page=6&per_page=10>; rel="last"'

Requests analysiert diese Header automatisch und bietet sie in leicht erreichbarer Form an::

    >>> r.links["next"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=2&per_page=10', 'rel': 'next'}

    >>> r.links["last"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=7&per_page=10', 'rel': 'last'}

Transport-Adapter
-----------------

Mit dem Erreichen der Version 1.0.0 wurde Requests auf ein modulares internes Design umgestellt.
Mit ein Grund für diese Umstellung war die Implementierung von Transportadaptern, die ursprünglich
`hier beschrieben`_ wurden. Transportadapter bieten einen Mechanismus zu Definition von Interaktionsmethoden
für einen HTTP-basierten Dienst. Im speziellen erlauben sie die Konfiguration auf Dienstebasis.

Requests wird mit einem einzelnen Transportadapter ausgeliefert, dem 
:class:`HTTPAdapter <requests.adapters.HTTPAdapter>`. Dieser Adapter bietet die Standardinteraktion
mit HTTP und HTTPs über die mächtige Bibliothek `urllib3`_. Immer dann, wenn eine
Requests :class:`Session <Session>` initialisiert wird, wird eine davon an das :class:`Session <Session>` 
Objekt für HTTP und eine für HTTPS gebunden.

Requests ermöglicht es Benutzern, ihre eigenen Transportadapter zu erstellen und zu benutzen,
die spezielle Funktionen bereit stellen. Einmal erzeugt, kann ein Transportadapter an ein
Session-Objekt gebunden werden, zusammen mit der Informationen, bei welchen Webdiensten es angewendet
werden soll.

::

    >>> s = requests.Session()
    >>> s.mount('http://www.github.com', MyAdapter())

Der ``mount``-Aufruf registriert eine spezielle Instanz eines Transportadapters für einen
Domänen-Prefix. Nachdem die Bindung erfolgt ist, wird jede HTTP-Anforderung einer Session,
deren URL mit dem Domänen-Prefix beginnt, den angegebenen Transportadapter benutzen.

Die Implementierung von Transportadaptern ist nicht Gegenstand dieser Dokumentation, aber
ein guter Startpunkt wäre es, von der Klasse ``requests.adapters.BaseAdapter`` zu erben..

.. _`hier beschrieben`: http://kennethreitz.org/exposures/the-future-of-python-http
.. _`urllib3`: https://github.com/shazow/urllib3

