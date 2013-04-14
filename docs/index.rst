.. Requests documentation master file, created by
   sphinx-quickstart on Sun Feb 13 23:54:25 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Requests: HTTP for Menschliche Wesen
====================================

Release v\ |version|. (:ref:`Installation <install>`)

Requests ist eine :ref:`Apache2 lizensierte <apache2>` HTTP Bibliothek,
geschrieben in Python, für die Benutzung durch Menschen.


Das **urllib2** Standard-Modul in Python bietet Ihnen die meisten HTTP-Funktionalitäten, die Sie
benötigen, aber die API ist **definitiv kaputt**.
Sie wurde für eine andere Zeit geschrieben -  und ein anderes Web. Sie erfordert eine *enorme* Menge
an Arbeit (inkl. dem Überschreiben von Methoden), um auch nur die einfachsten Dinge zu erledigen.

So sollten die Dinge nicht laufen. Nicht in Python.

::

    >>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
    >>> r.status_code
    200
    >>> r.headers['content-type']
    'application/json; charset=utf8'
    >>> r.encoding
    'utf-8'
    >>> r.text
    u'{"type":"User"...'
    >>> r.json()
    {u'private_gists': 419, u'total_private_repos': 77, ...}

Der gleiche Code, aber `ohne Requests <https://gist.github.com/973705>`_.


Requests nimmt Ihnen die ganze harte Arbeit für HTTP/1.1 in Python ab - und macht damit die Integration von
Webdiensten schnell und einfach. Keine Notwendigkeit, manuell query strings zu Ihren URLs hinzu zu fügen
oder die Daten für ein POST erst in form-encoding umzuwandeln. Keep-Alive und das Pooling von Verbindungen
laufen 100% automatisch ab, erledigt von `urllib3 <https://github.com/shazow/urllib3>`_, die in Requests
eingebettet ist.


Stimmen zu Requests
-------------------

Die Regierung Ihrer Majestät, Amazon, Google, Twilio, Mozilla, Heroku, PayPal, NPR, Obama for America, Transifex, Native Instruments, The Washington Post, Twitter, SoundCloud, Kippt, Readability, and US-Bundesbehörden benutzen Requests
in Ihren internen Anwendungen. Requests wurde mehr als 2 Millionen mal von PyPI herunter geladen.

**Armin Ronacher**
    Requests ist das perfekte Beispiel dafür, wie elegant eine API mit dem richtigen Grad
    an Abstraktion sein kann.

**Matt DeBoard**
    I werde mir @kennethreitz's Python requests Modul auf meinen Körper tätowieren lassen.
    Das ganze Ding.

**Daniel Greenfeld**
    Ich habe eine 1200 Zeilen Bibliothek aus Spaghetticode überflüssig gemacht und durch 
    10 Zeilen Code ersetzt, dank @kennethreitz's request Bibliothek. 
    Heute war ein genialer Tag.

**Kenny Meyers**
    Python HTTP: Im Zweifel, ach was, auch ohne Zweifel, benutzen Sie Requests.
    Elegant, einfach, Pythonisch.



Features
--------

- Internationale Domains und URLs
- Keep-Alive & Connection Pooling
- Sessions mit persistierten Cookies
- SSL-Verifizierung wie im Browser
- Basic/Digest Authentifizierung
- Elegante Schlüssel/Wert Cookies
- Automatische Dekomprimierung
- Unicode Antwortdaten
- Hochladen von mehrteiligen Dateien
- Verbindungs-Timeouts
- ``.netrc`` Unterstützung
- Python 2.6-3.3
- Thread-Sicherheit


Benutzeranleitung
-----------------

Dieser Teil der Dokumentation, der zum Großteil aus Beschreibungsprosa besteht, beginnt 
mit einigen Hintergrundinformationen über Requests. Danach liegt der Schwerpunkt auf einer
Schritt-für-Schritt Anleitung, um den maximalen Nutzen aus Requests zu ziehen.

.. toctree::
   :maxdepth: 2

   user/intro
   user/install
   user/quickstart
   user/advanced
   user/authentication


Community Guide
-----------------

Dieser Abschnitt der Dokumentation befasst sich mit dem Requests-Ökosystem und der
Nutzergemeinde.

.. toctree::
   :maxdepth: 1

   community/faq
   community/out-there.rst
   community/support
   community/updates

API Dokumentation
-----------------

Falls Sie nach Informationen zu einer bestimmten Funktion, Klasse oder Methode suchen,
dann ist dies der richtige Abschnitt der Dokumentation.

.. toctree::
   :maxdepth: 2

   api


Hinweise für Mitmacher
----------------------

Wenn Sie sich am Projekt beteiligen wollen, ist dies der Teil der Dokumentation, 
den Sie lesen sollten.

.. toctree::
   :maxdepth: 1

   dev/philosophy
   dev/internals
   dev/todo
   dev/authors
