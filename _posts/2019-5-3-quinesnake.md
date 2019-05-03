---
layout: post
title: Un quine que juega a la serpiente en su código fuente
categories: desarrollo
---

![quinesnake](https://github.com/taylorconor/quinesnake/raw/master/animation.gif)

Un [quine](https://es.wikipedia.org/wiki/Quine_(programa)) es un programa que produce su propio código fuente como salida. Un ejemplo en código C sería el siguiente:

```c
main() { char *s="main() { char *s=%c%s%c; printf(s,34,s,34); }"; printf(s,34,s,34); } 
```

El quine que os presento hoy se llama [quinesnake](https://github.com/taylorconor/quinesnake) y se trata de un programa para jugar al famoso juego de la serpiente sobre su propio código fuente. El programa se compila solo, por lo que sólo tendremos que hacer ejecutable el código fuente y ejecturlo (el programa invoca a `g++` cuando arranca). Para controlar a la serpiente usaremos las teclas `w`, `a`, `s` y `d`.

El código fuente lo encontráis en [Github](https://github.com/taylorconor/quinesnake/blob/master/quinesnake.cpp) y tiene una pinta tal que así:

```c
...
s.erase(e,1);s.replace(s.find(L"%d"),2,L"("+s+L")");initscr();start_color();for(
e=0;e<3;){init_pair(e,0,e+9);C(2,e++,1);}noecho();M();while(T())usleep(z<<7);end
win();})x";I G(I x,I y){K 3&s[y*w+x]>>8;}I C(I x,I y,I c){(s[y*w+x]&=~z)|=c<<8;}
...
```


Si queréis una versión más legible del código, y que además esté comentada, podéis encontrarla [aquí](https://github.com/taylorconor/quinesnake/blob/master/quinesnake-commented.cpp).


