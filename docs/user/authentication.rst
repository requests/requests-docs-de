.. _authentication:

Authentifizierung
=================

Dieses Dokument behandelt verschiedene Arten der Authentifizierung mit Requests.

Viele Webdienste erforderne eine Anmeldung und es gibt eine ganze Reihe verschiedener Typen.
Nachfolgend diskutieren wir verschiedene Formen der Authentifizierung, die in Requests verfügbar sind,
vom Einfachen zum Komplexen.


Basic Authentication
--------------------

Viele Webdienste, die eine Anmeldung erfordern, akzeptieren HTTP Basic Auth. Dies ist die einfachste
Art und Weise und Requests unterstützt dies direkt.

Anforderungen mit HTTP Basic Auth zu senden, ist sehr einfach::

    >>> from requests.auth import HTTPBasicAuth
    >>> requests.get('https://api.github.com/user', auth=HTTPBasicAuth('user', 'pass'))
    <Response [200]>

tatsächlich ist HTTP Basic Auth so verbreitet, dass Requests dafür ein nützliches Kürzel zur Verfügung stellt::

    >>> requests.get('https://api.github.com/user', auth=('user', 'pass'))
    <Response [200]>

Die Anmeldedaten in einem Tupel wie diesem bereit zu stellen ist exakt das Gleiche als in dem 
``HTTPBasicAuth`` Beispiel oben.


Digest Authentication
---------------------

Eine andere sehr verbreitete Art der HTTP Authentifizierung ist Digest Authentication,
die ebenfalls von Requests direkt unterstützt wird::

    >>> from requests.auth import HTTPDigestAuth
    >>> url = 'http://httpbin.org/digest-auth/auth/user/pass'
    >>> requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
    <Response [200]>


Andere Authentifizierungsarten
------------------------------

Requests wurde so entworfen, dass andere Formen der Authentifizierung leicht schnell
hinzugefügt werden können. Mitglieder der Open-Source Gemeinschaft schreiben oft
Authentifizierungs-Handler für komplexere oder weniger verbreitete Formen der Anmeldung.
Einige der besten wurden unter der `Requests organization`_ zusammengefasst, darunter:

- OAuth_
- Kerberos_
- NTLM_

Falls Sie eine dieser Authentifizierungen benutzen wollen, gehen Sie einfach zu ihrer
Github-Seite und folgen Sie den Anweisungen.


Neue Formen der Authentifizierung
---------------------------------

Falls Sie keine gute Implementierung für die Anmeldung, die Sie nutzen wollen, finden können,
können Sie diese selbst implementieren. Requests macht es Ihnen leicht, eigene Authentifizierungsmodule
hinzuzufügen.

Dazu leiten Sie von der Klasse :class:`requests.auth.AuthBase` ab und implementieren den Aufruf der
``__call__()`` Methode. Wenn ein Authentifizierungs-Handler an eine Anforderung gebunden ist,
wird er während des Setups der Anforderung aufgerufen. Die ``__call__`` Methode muss daher alles,
was für das Gelingen der Anmeldung nötig ist, implementieren. Einige Formen der Authentifizierung
stellen zusätzlich hooks für zusätzliche Funktionalitäten bereit.

Beispiele dazu finden Sie unter `Requests organization`_ und in der Datei ``auth.py``.

.. _OAuth: https://github.com/requests/requests-oauthlib
.. _Kerberos: https://github.com/requests/requests-kerberos
.. _NTLM: https://github.com/requests/requests-ntlm
.. _Requests organization: https://github.com/requests

