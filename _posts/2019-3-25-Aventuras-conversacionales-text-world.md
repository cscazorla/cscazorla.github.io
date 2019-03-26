---
layout: post
title: Aventuras conversacionales con Microsoft TextWorld
categories: videojuegos
---

¿Sois del selecto grupo de personas que crecísteis con las aventuras conversacionales? ¿Disfrutásteis durante horas y horas tecleando comandos en [La Aventura Original](http://wiki.caad.es/La_Aventura_Original), [Zork](https://www.caad.es/fichas/zork-en-espanol.html), [El Quijote](https://tabernadegrog.blogspot.com/2019/02/don-quijote-aventura-conversacional.html) o [Megacorp](http://computeremuzone.com/ficha.php?id=136)?

![La Aventura Original]({{ site.baseurl }}/assets/img/aventura-original.jpg)

Si habéis respondido _sí_, y además siempre habéis soñado con poder desarrollar vosotros mismos una aventura conversacional, tenéis que probar [TextWorld](https://textworld.readthedocs.io/en/latest/index.html). Esta herramienta Open-Source desarrollada en Python por [Microsoft](https://www.microsoft.com/en-us/research/project/textworld/) permite generar y simular aventuras conversacionales. 

![La Aventura Original](https://textworld.readthedocs.io/en/latest/_images/TextWorld.png)

A partir de una serie de parámetros como el tamaño del mapa, número de objetos, extensión de la aventura, complejidad, etc. permite generar aventuras y jugarlas. Por ejemplo, para generar un mapa y en la carpeta `tw_games` sólo tendríamos que ejecutar en la consola: 

```javascript
tw-make custom --world-size 5 --nb-objects 10 --quest-length 5 --seed 1234 --output tw_games/custom_game.ulx
```

Para jugar a este juego sólo tendríamos que ejecutarlo mediante:

```
tw-play tw_games/custom_game.ulx
```



Por cierto, si os interesa este mundillo no dejéis de leer este fabuloso [artículo de John Tones en Xataka](https://www.xataka.com/literatura-comics-y-juegos/en-el-principio-fue-la-aventura-conversacional)