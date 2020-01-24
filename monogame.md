---
layout: page
title: "Monogame"
---
The [Monogame platform](http://www.monogame.net/) is the open-source successor to Microsoft's XNA platform.  It is essentially a combination of managed Direct-X wrapper, game framework, and game development support libraries.  It has further been expanded with cross-platform support targeting Linux, MacOS, Andriod, IOS, XBox One, PS4, PSVita, and Switch.

Monogame is _not_ a game engine, rather it is a toolkit for building a game engine.  It provides powerful abstractions around the low-level details of hardware rendering and provides a straightforward [Game Loop](https://gameprogrammingpatterns.com/game-loop.html) implementation.

Monogame can be downloaded from [http://www.monogame.net/downloads](http://www.monogame.net/documentation/) and its documentation is found at [http://www.monogame.net/documentation/](http://www.monogame.net/documentation/)

## Topics

{% for topic in site.monogame-topics %}
*  [{{topic.title}}]({{site.baseurl}}{{ topic.url }})
{% endfor %}
