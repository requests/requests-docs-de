Entwicklungsphilosophie
=======================

Requests ist eine offene, aber etwas eigenwillige Bibliothek, entwickelt von einem offenen, 
aber etwas eigenwilligen Entwickler.


Wohlwollender Diktator
~~~~~~~~~~~~~~~~~~~~~~

`Kenneth Reitz <http://kennethreitz.org>`_ ist der `BDFL <http://de.wikipedia.org/wiki/Benevolent_Dictator_for_Life>`_. 
Er hat das letzte Wort hinsichtlich jedes Aspekts von Requests.


Werte
~~~~~

- Einfachheit ist immer besser als Funktionalität.
- Hör jedem zu und dann ziehe es nicht in Betracht..
- Die API ist alles, was zählt. Alles andere ist zweitrangig.
- Kümmere Dich um die 90% der Anwendungsfälle. Ignoriere die Neinsager.


Semantische Versionierung
~~~~~~~~~~~~~~~~~~~~~~~~~

Über viele Jahre wurde die Open-Source Gemeinschaft von der Versionsnummer-Störung heimgesucht. Die Nummern unterscheiden
sich von Projekt zu Projekt so grundlegend, dass diese Information mehr oder weniger nutzlos ist.

Requests benutzt `Semantische Versionierung <http://semver.org>`_. Dieses Spezifikation versucht, diesem Wahnsinn mit einem
kleinen Satz an praxisorientierten Regeln ein Ende zu bereiten. 

Standardbibliothek?
~~~~~~~~~~~~~~~~~~~

Es gibt keinen *aktiven* Plan, dass Requests Teil der Standardbilbiothek wird. Diese Entscheidung wurde intensiv mit Guido und vielen anderen Kernentwicklern besprochen.

Zusammengefasst ist die Standardbibliothek der Platz, an den eine Bibliothek zum Sterben geht. Es ist für ein Modul dann 
angebracht, in die Standardbibliothek aufgenommen zu werden, wenn eine aktive Entwicklung nicht länger nötig ist.

Requests hat jetzt gerade knapp die Version 1.0.0 hinter sich gelassen. Diese große Meilenstein ist ein wichtiger Schritt
in die richtige Richtung.


Linux Distributionen
~~~~~~~~~~~~~~~~~~~~

Distributionen wurde für viele Linux Repositories erstellt, einschließlich Ubuntu, Debian, RHEL, and Arch.

These Distributionen sind manchmal abweichende Forks or werden auf andere Weise nicht auf dem aktuellen Stand der Codebasis und der Bugfixes gehalten. PyPI (und seine Mirrors) und GitHub sind die offiziellen Distributionsquellen; Alternativen werden vom Requests-Projekt nicht unterstützt.
