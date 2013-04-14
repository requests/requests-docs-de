krTheme Sphinx Style
====================

Dieses Repository enthält die sphinx Stylesheets, die Kenneth Reitz in den meisten
seiner Projekte benutzt. Sie sind eine Ableitung von Mitsuhiko's Themes für Flask 
und Flask-orientierte Projekte. Um diesen Stil in Ihrer Sphinx-Dokumentation zu benutzen,
folgen Sie dieser Anleitung:

1. kopieren Sie diesen Ordner als _themes in Ihren docs-Ordner. 
   Alternativ können Sie auch git submodules benutzen, um den Inhalt hier auszuchecken.

2. fügen Sie das hier zu Ihrer conf.py hinzu: ::

    sys.path.append(os.path.abspath('_themes'))
    html_theme_path = ['_themes']
    html_theme = 'flask'

Folgende Themes existieren:

**kr**
    das standard flask Dokumentations-Theme für grpße Projekte

**kr_small**
    small one-page theme.  Dafür gedacht, mit sehr kleinen Zusatzbilbiotheken benutzt zu werden.

