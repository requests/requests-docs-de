.. _api:

Entwickler-Schnittstelle
========================

.. module:: requests

Dieser Teil der Dokumentation deckt alle Schnittstellen von Requests ab.
Für die Teile von Requests, die externe Bibliotheken benötigen, dokumentieren
wir die wichtigsten hier an dieser Stelle und geben Links zur eigentlichen Dokumentation an.


Haupt-Schnittstelle
-------------------

Die gesamte Funktionalität von Requests kann mit diesen 7 Methoden erreicht werden.
Alle liefern eine Instanz der Klasse :class:`Respsonse <Response>`_.

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
      r.json()   # This *call* raises an exception if JSON decoding fails

* The ``Session`` API has changed. Sessions objects no longer take parameters.
  ``Session`` is also now capitalized, but it can still be
  instantiated with a lowercase ``session`` for backwards compatibility.

  ::

      s = requests.Session()    # formerly, session took parameters
      s.auth = auth
      s.headers.update(headers)
      r = s.get('http://httpbin.org/headers')

* All request hooks have been removed except 'response'.

* Authentication helpers have been broken out into separate modules. See
  requests-oauthlib_ and requests-kerberos_.

.. _requests-oauthlib: https://github.com/requests/requests-oauthlib
.. _requests-kerberos: https://github.com/requests/requests-kerberos

* The parameter for streaming requests was changed from ``prefetch`` to
  ``stream`` and the logic was inverted. In addition, ``stream`` is now
  required for raw response reading.

  ::

      # in 0.x, passing prefetch=False would accomplish the same thing
      r = requests.get('https://github.com/timeline.json', stream=True)
      r.raw.read(10)

* The ``config`` parameter to the requests method has been removed. Some of
  these options are now configured on a ``Session`` such as keep-alive and
  maximum number of redirects. The verbosity option should be handled by
  configuring logging.

  ::

      # Verbosity should now be configured with logging
      my_config = {'verbose': sys.stderr}
      requests.get('http://httpbin.org/headers', config=my_config)  # bad!


Licensing
~~~~~~~~~

One key difference that has nothing to do with the API is a change in the
license from the ISC_ license to the `Apache 2.0`_ license. The Apache 2.0
license ensures that contributions to requests are also covered by the Apache
2.0 license.

.. _ISC: http://opensource.org/licenses/ISC
.. _Apache 2.0: http://opensource.org/licenses/Apache-2.0

