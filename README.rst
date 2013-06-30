Requests: HTTP für menschliche Wesen
====================================


.. image:: https://travis-ci.org/kennethreitz/requests.png?branch=master
        :target: https://travis-ci.org/kennethreitz/requests

Requests ist eine Apache2 lizensierte HTTP Bibliothek, geschrieben in Python, für menschliche Wesen.

Die meisten existierenden Python-Module zum Senden von HTTP-Anfragen sind extrem
detailorientiert und mühsam zu benutzen. Pythons integriertes Modul urllib2 bietet die
meisten Funktionen, die Sie wahrscheinlich benötigen, aber die API ist definitiv kaputt.
Sie erfordert eine Unmenge von Arbeit (einschließlich des Überschreibens von Methoden),
um die einfachsten Dinge zu erledigen.

So sollten die Dinge nicht laufen. Nicht in Python.

.. code-block:: 

    >>> r = requests.get('https://api.github.com', auth=('user', 'pass'))
    >>> r.status_code
    204
    >>> r.headers['content-type']
    'application/json'
    >>> r.text
    ...

Sehen Sie sich den gleichen Code, `aber ohne Requests <https://gist.github.com/973705>`_ an.

Requests erlaubt Ihnen das Versenden von HTTP/1.1-Anfragen. Sie können Header, Formulardaten,
mehrteilige Dateien und Parameter über einfache Python-Dictionaries hinzufügen. Auf die gleiche
Art und Weise greifen Sie auch auf die Daten der Antwort zu. Unter der Haube steckt die httplib
und `urllib3 <https://github.com/shazow/urllib3>`_ , aber Requests übernimmt all die harte Arbeit
und die verrückten Hacks für Sie.


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


Installation
------------

Zum Installieren von Requests, führen Sie einfach:

.. code-block:: bash

    $ pip install requests

aus. Oder, wenn Sie das unbedingt müssen:

.. code-block:: bash

    $ easy_install requests

Aber Sie sollten das wirklich nicht machen.



Mitarbeiten
-----------

#. Suchen Sie nach offenen Tickets oder erstellen Sie selbst ein Ticket, um eine Diskussion über ein neues Feature oder einen Bug zu starten. Es gibt ein Tag Contributor Friendly für Tickets, die ideal für Leute sind, die mit der Codebasis noch nicht so vertraut sind.
#. Spalten Sie `das Repository`_ auf Github ab und beginnen Sie damit, Ihre Änderungen im **master** branch (oder erstellen Sie davon einen neuen Branch).
#. Schreiben Sie einen Test, der zegit, dass der Bug behoben wurde oder das neue Feature wie erwartet funktioniert.
#. Senden Sie einen pull request und gehen Sie dem Maintainer so lange auf die Nerven, bis die Änderungen zusammengeführt und publiziert wurden. :) Stellen Sie sicher, dass Sie sich unter AUTHORS_ hinzugefügt haben.

.. _`das Repository`: http://github.com/kennethreitz/requests
.. _AUTHORS: https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst
