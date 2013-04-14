.. _api:

Entwickler-Schnittstelle
========================

.. module:: requests

Dieser Teil der Dokumentation deckt alle Schnittstellen von Requests ab.
Für die Teile von Requests, die externe Bibliotheken benötigen, dokumentieren
wir die wichtigsten hier an dieser Stelle und geben Links zur eigentlichen Dokumentation an.


Haupt-Schnittstelle
-------------------

Die gesamte Funktionalität von Requests kann mit den folgenden sieben Methoden erreicht werden.
Alle liefern eine Instanz der Klasse :class:`Response <Response>`_ zurück.

.. autofunction:: request

.. autofunction:: head
.. autofunction:: get
.. autofunction:: post
.. autofunction:: put
.. autofunction:: patch
.. autofunction:: delete


Untergeordnete Klassen
~~~~~~~~~~~~~~~~~~~~~~

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: Response
   :inherited-members:

Request Sessions
----------------

.. autoclass:: Session
   :inherited-members:

.. autoclass:: requests.adapters.HTTPAdapter
   :inherited-members:


Exceptions
~~~~~~~~~~

.. module:: requests

.. autoexception:: RequestException
.. autoexception:: ConnectionError
.. autoexception:: HTTPError
.. autoexception:: URLRequired
.. autoexception:: TooManyRedirects


Nachschlagen von Statuscodes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. autofunction:: requests.codes

::

    >>> requests.codes['temporary_redirect']
    307

    >>> requests.codes.teapot
    418

    >>> requests.codes['\o/']
    200

Cookies
~~~~~~~

.. autofunction:: dict_from_cookiejar
.. autofunction:: cookiejar_from_dict
.. autofunction:: add_dict_to_cookiejar


Encodings
~~~~~~~~~

.. autofunction:: get_encodings_from_content
.. autofunction:: get_encoding_from_headers
.. autofunction:: get_unicode_from_response
.. autofunction:: decode_gzip


Klassen
~~~~~~~

.. autoclass:: requests.Response
   :inherited-members:

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: requests.PreparedRequest
   :inherited-members:

.. _sessionapi:

.. autoclass:: requests.Session
   :inherited-members:

.. autoclass:: requests.adapters.HTTPAdapter
   :inherited-members:


Migration auf 1.x
-----------------

Dieser Abschnitt zeigt die Hauptunterschiede zwischen 0.x und 1.x auf und 
ist dazu gedacht, den Schmerz eines Upgrades möglichst gering zu halten.


Änderungen der API
~~~~~~~~~~~~~~~~~~

* ``Response.json`` ist jetzt ein callable und nicht länger eine Eigenschaft einer Serverantwort.

  ::

      import requests
      r = requests.get('https://github.com/timeline.json')
      r.json()   # Dieser *Aufruf* wirft eine Exception, sollte die JSON-Dekodierung fehlschlagen

* Die ``Session`` API hat sich geändert. Session-Objekte erhalten keine Parameter mehr.
  ``Session``wird jetzt auch mit großem Anfagnsbuchstaben geschrieben, aber das Objekt
  kann aus Kompatibilitätsgründen weiterhin klein geschrieben als ``session`` instantiiert werden.
  ::

      s = requests.Session()    # früher konnten Parameter übergeben werden
      s.auth = auth
      s.headers.update(headers)
      r = s.get('http://httpbin.org/headers')

* Alle Hooks für Anfragen wurden entfernt, mit Ausnahme von 'response'.

* Die Hilfsroutinen für die Authentifizierung wurden in separate Module ausgegliedert.
  Sehen Sie sich dazu requests-oauthlib_ and requests-kerberos_ an.

.. _requests-oauthlib: https://github.com/requests/requests-oauthlib
.. _requests-kerberos: https://github.com/requests/requests-kerberos

* Der Parameter für Streaming-Anfragen wurde von ``prefetch`` in ``stream`` geändert
  und die Logik invertiert. Zusätzlich muss ``stream`` jetzt angegeben werden, um die
  Rohdaten der Serverantwort zu lesen.

  ::

      # in 0.x, wurde das gleiche Ergebnis mit der Angaben von prefetch=False erreicht
      r = requests.get('https://github.com/timeline.json', stream=True)
      r.raw.read(10)

* Der ``config`` Parameter für Anfragen wurde entfernt. Einige der davon betroffenen
  Optionen werden jetzt in einer ``Session`` konfiguriert, wie z.B. keep-alive und 
  die meximale Anzahl von Redirects. Die Option für ausführliche Informationen (verbosity)
  sollte über die Konfiguration des Loggings gesetzt werden.

  ::

      # Ausfürhliche Informationen sollten jetzt über das Logging konfiguriert werden
      my_config = {'verbose': sys.stderr}
      requests.get('http://httpbin.org/headers', config=my_config)  # schlecht!


Lizensierung
~~~~~~~~~~~~

Ein Hauptunterschied, der nichts mit der API zu tun hat, ist eine Änderung der Lizensierung
von der ISC_ Lizenz zur `Apache 2.0`_ Lizenz. Die Apache 2.0 Lizenz stellt sicher, dass
Beiträge zu Requests ebenfalls von der Apache 2.0 Lizenz abgedeckt sind.

.. _ISC: http://opensource.org/licenses/ISC
.. _Apache 2.0: http://opensource.org/licenses/Apache-2.0

