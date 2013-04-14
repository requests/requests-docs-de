Wie helfen?
===========

Requests wird aktiv entwickelt und Beiträge sind mehr als willkommen!

#. Suchen Sie nach offenen Tickets oder erstellen Sie selbst ein Ticket, um eine Diskussion über ein neues Feature 
   oder einen Bug zu starten. Es gibt ein Tag Contributor Friendly für Tickets, die ideal für Leute sind, 
   die mit der Codebasis noch nicht so vertraut sind.
#. Spalten Sie `das Repository <https://github.com/kennethreitz/requests>`_ auf Github ab und beginnen Sie damit,
   Ihre Änderungen im **master** branch (oder erstellen Sie davon einen neuen Branch).
#. Schreiben Sie einen Test, der zegit, dass der Bug behoben wurde oder das neue Feature wie erwartet funktioniert.
#. Senden Sie einen pull request und gehen Sie dem Maintainer so lange auf die Nerven, bis die Änderungen 
   zusammengeführt und publiziert wurden. :) 
   Stellen Sie sicher, dass Sie sich unter `AUTHORS  <https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst>`_ hinzugefügt haben.


Feature Freeze
--------------

Mit der Version 1.0.0 wurde die Erstellung neuer Features für Requests eingefroren.
Anforderungen für neue Features und pull requests, die neue Features implementieren,
werden nicht akzeptiert.


Entwicklungs-Abhängigkeiten
---------------------------

Um die Requests Testsuite auszuführen, mpssen Sie py.test installieren::

    $ pip install -r requirements.txt
    $ invoke test
    py.test
    platform darwin -- Python 2.7.3 -- pytest-2.3.4
    collected 25 items

    test_requests.py .........................
    25 passed in 3.50 seconds


Laufzeitumgebungen
------------------

Requests unterstützt derzeit die folgenden Versionen von Python:

- Python 2.6
- Python 2.7
- Python 3.1
- Python 3.2
- Python 3.3
- PyPy 1.9

Unterstützung für Python 3.1 und 3.2 kann zu jeder Zeit wegfallen.

Die Google App Engine wird nie offiziell unterstützt werden. Pull requests aus Gründen der Kompatibilität werden akzeptiert, so lange sie die Codebasis nicht verkomplizieren.


Sind Sie verrückt?
--------------

- Unterstüzung für SPDY wäre großartig. Keine C extensions.
