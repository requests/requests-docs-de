.. _install:

Installation
============

Dieser Teil der Dokumentation behandelt die Installation von Requests.
Der erste Schritt zur Benutzung jeder Software ist es, diese korrekt zu installieren.


Distribute & Pip
----------------

Die Installation von Requests funktioniert sehr einfach über `pip <http://www.pip-installer.org/>`_::

    $ pip install requests

order mit `easy_install <http://pypi.python.org/pypi/setuptools>`_::

    $ easy_install requests

Aber das sollten Sie `wirklich nicht machen <http://www.pip-installer.org/en/latest/other-tools.html#pip-compared-to-easy-install>`_.



Cheeseshop Mirror
-----------------

Falls der Cheeseshop offline ist, können Sie Requests von einem der Mirrors installieren.
`Crate.io <http://crate.io>`_ ist einer von ihnen::

    $ pip install -i http://simple.crate.io/ requests


Holen Sie sich den Code
-----------------------

Requests wird aktiv auf Guthub entwickelt, wo der Code 
`immer verfügbar ist <https://github.com/kennethreitz/requests>`_.

Sie können entweder das öffentliche Repository clonen::

    git clone git://github.com/kennethreitz/requests.git

Laden Sie den `tarball <https://github.com/kennethreitz/requests/tarball/master>`_ herunter::

    $ curl -OL https://github.com/kennethreitz/requests/tarball/master

Oder sie laden den `zipball <https://github.com/kennethreitz/requests/zipball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/zipball/master


Sobald Sie eine Kopie des Quellcodes haben, können Sie diesen in Ihr Python-Package einbetten oder
einfach zu Ihren site-packages hinzufügen::

    $ python setup.py install
