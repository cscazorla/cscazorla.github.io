---
layout: post
title: Creando un sistema de partículas
categories: videojuegos
---

Hoy os quiero enseñar lo sencillo que es hacer un sistema de partículas que os permita hacer efectos de humo, explosiones, o cualquier otra idea que se os ocurra. Utlizaremos el lenguaje de programación *Javascript*, para que todos podáis probarlo en vuestro navegador y no sea necesario instalar ningún software. No os preocupéis, no necesitamos ningún doctorado en matemáticas o física para generar este sistema :) 

![Sistema de partículas]({{ site.baseurl }}/assets/img/sistema-particulas.png)

Todo el [código lo podéis encontrar al final del artículo](#código-fuente). Se trata únicamente de dos ficheros:

- `index.html` - HTML para situar el canvas en la pantalla.
- `main.js` - Código Javascript para generar el sistema de partículas.

Para ejecutar la animación sólo tenéis que abrir `index.html` con cualquier navegador. Este código está basado en las demos [PlayfulJS de Hunter Loftis](https://github.com/hunterloftis/playfuljs-demos) al que he añadido algunos cambios.

## Objetivo

Nuestro objetivo es crear un _motor_ que mueva partículas por la pantalla hacia un punto determinado. En nuestro caso, ese punto será el cursor del ratón. Al iniciar la simulación, cada una de las partículas (situadas inicialmente en puntos aleatorios de la pantalla) se verán empujadas hacia la posición donde esté el ratón. Por tanto, lo que tendremos que implementar es un bucle (_game loop_) que en cada _frame_  

1. pinte puntos/partículas por la pantalla, 
2. calcule el vector velocidad (dirección y módulo) de cada partícula,
3. y avance _frame_ a _frame_ la simulación.

Estos tres pasos se ejecutarán (para todo el array de partículas) en nuestro _game loop_ de la siguiente manera:

{% highlight javascript %}
for (var i = 0; i < particles.length; i++) {
  particles[i].attract(mouse.x, mouse.y);
  particles[i].integrate();
  particles[i].draw();
}
{% endhighlight %}

En cada frame de nuestra simulación se llamarán a tres funciones:

- `attract()` - Calcula cuál es la dirección y módulo de velocidad necesaria para cada partícula para alcanzar el punto destino (posición del ratón.)
- `integrate()` - Conocidas las coordenadas actuales y las que se quiere desplazar, realiza los cálculos de integración (posición -> velocidad -> aceleración).
- `draw()` - Dibuja la partícula en su nueva posición en la pantalla.

## El sistema de partículas

Lo primero es definir una entidad en Javascript que nos permita gestionar cada partícula. En este caso se ha definido un objeto _Particle_ con dos atributos: su coordenada X e Y en la pantalla.

{% highlight javascript %}
function Particle(x, y) {
    this.x = this.oldX = x;
    this.y = this.oldY = y;
}
{% endhighlight %}

Inicialmente, como las partículas no se mueven, la posición anterior y la actual (tras la ejecución de un frame), será la misma.

Al arrancar la simulación podremos inicializar (en posiciones aleatorias de la pantalla) tantas partículas como queramos mediante el siguiente bucle:

{% highlight javascript %}
for (var i = 0; i < NUMBER_OF_PARTICLES; i++) {
    particles[i] = new Particle(Math.random() * width, Math.random() * height);
}
{% endhighlight %}

## El bucle principal

### 1. Seguimiento del ratón

En esta simulación las partículas siguen la posición del ratón en la pantalla en cada frame. El efecto conseguido es el de un _enjambre_ de partículas siguiendo a un punto.

Como veíamos anteriormente en el bucle principal, a la función `attract()` se le pasan como parámetros las coordenadas X e Y actuales del ratón. El código de esta función es el siguiente:

{% highlight javascript %}
Particle.prototype.attract = function (x, y) {
    var dx = x - this.x;
    var dy = y - this.y;
    var distance = Math.sqrt(dx * dx + dy * dy);
    this.x += dx / distance;
    this.y += dy / distance;
};
{% endhighlight %}

La distancia desde el punto actual de la partícula, al punto que queremos dirigirnos (la posición del ratón), podremos calcularla mediante el módulo del vector que une dichos puntos

![Distancia entre dos puntos](http://www.rasmus.is/uk/t/F/Su58k03_m02.gif)

Actualizamos las variables locales `this.x` y `this.y` (que representan la posición que queremos alcanzar en el siguiente frame) con los valores deseados. La división por `distance` provoca que las partículas más cercanas al ratón se desplacen con mayor aceleración (podemos verlo como una fuerza de atracción mayor)

### 2. Mover las partículas

Tenemos que enseñar a nuestras partículas a moverse por la pantalla. En cada frame hay que decir a cada partícula la nueva dirección y módulo de su velocidad. En el paso anterior calculamos en las variables locales `x` e `y` de cada partícula la nueva coordenada a la que queremos que se desplace. Para poder conseguir una velocidad (variación de posición / variación de tiempo), necesitamos integrar.

Un recurso sencillo, y que nos evita tener que integrar, es utilizar la [Integración de Verlet](https://es.wikipedia.org/wiki/Integraci%C3%B3n_de_Verlet). Lo que hacemos es comparar la posición actual de la partícula (la que hemos calculado en el paso anterior y que queremos alcanzar) respecto a su posición en el _frame_ anterior (la que tiene actualmente). 

Nuestro proceso de integración de la posición de la partícula de un _frame_ a otro se realiza mediante la siguiente función:

{% highlight javascript %}
Particle.prototype.integrate = function () {
    var velocityX = (this.x - this.oldX) * DAMPING;
    var velocityY = (this.y - this.oldY) * DAMPING;
    this.oldX = this.x;
    this.oldY = this.y;
    this.x += velocityX;
    this.y += velocityY;
};
{% endhighlight %}

Las variables `velocityX` y `velocityY` almacenan la diferencia de posición en X e Y entre el frame actual y el frame anterior. Se multiplican por un coeficiente `DAMPING` para darle cierto toque de rozamiento. 

### 3. Pintar la partícula

El último paso es dibujar en pantalla la posición de la partícula. Para ello haremos uso de la siguiente función:

{% highlight javascript %}
Particle.prototype.draw = function () {
    if (COLOR_ENABLED) {
        ctx.strokeStyle = '#' + (0x1000000 + (Math.random()) * 0xffffff).toString(16).substr(1, 6);
    } else {
        ctx.strokeStyle = '#ffffff';
    }
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(this.oldX, this.oldY);
    ctx.lineTo(this.x, this.y);
    ctx.stroke();
};
{% endhighlight %}

Esta función se encarga de pintar el pixel en la pantalla y moverlo de la posición anterior (`this.oldx`,`this.oldy`) a la nueva posición (`this.x`,`this.y`).

`COLOR_ENABLED` es una variable de configuración para indicar si queremos pintar cada partícula de un color diferente. En caso contrario, todas se pintarán en blanco.

## Código fuente

### index.html

{% highlight html %}
<!doctype html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Sistema de Partículas</title>
</head>
<body style='background: #000; margin: 0; padding: 0; width: 100%; height: 100%;'>
    <canvas id='display' width='1' height='1' style='width: 100%; height: 100%;' />
    <script src="main.js"></script>
</body>
</html>
{% endhighlight %}


### main.js

{% highlight javascript %}
var DAMPING = 0.99999; 
var NUMBER_OF_PARTICLES = 300;
var COLOR_ENABLED = false;

function Particle(x, y) {
    this.x = this.oldX = x;
    this.y = this.oldY = y;
}

Particle.prototype.integrate = function () {
    var velocityX = (this.x - this.oldX) * DAMPING;
    var velocityY = (this.y - this.oldY) * DAMPING;
    this.oldX = this.x;
    this.oldY = this.y;
    this.x += velocityX;
    this.y += velocityY;
};

Particle.prototype.attract = function (x, y) {
    var dx = x - this.x;
    var dy = y - this.y;
    var distance = Math.sqrt(dx * dx + dy * dy);
    this.x += dx / distance;
    this.y += dy / distance;
};

Particle.prototype.draw = function () {
    if (COLOR_ENABLED) {
        ctx.strokeStyle = '#' + (0x1000000 + (Math.random()) * 0xffffff).toString(16).substr(1, 6);
    } else {
        ctx.strokeStyle = '#ffffff';
    }
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(this.oldX, this.oldY);
    ctx.lineTo(this.x, this.y);
    ctx.stroke();
};

var display = document.getElementById('display');
var ctx = display.getContext('2d');
var particles = [];
var width = display.width = window.innerWidth;
var height = display.height = window.innerHeight;
var mouse = { x: width * 0.5, y: height * 0.5 };

for (var i = 0; i < NUMBER_OF_PARTICLES; i++) {
    particles[i] = new Particle(Math.random() * width, Math.random() * height);
}

display.addEventListener('mousemove', onMousemove);

function onMousemove(e) {
    mouse.x = e.clientX;
    mouse.y = e.clientY;
}

requestAnimationFrame(frame);

function frame() {
    requestAnimationFrame(frame);
    ctx.clearRect(0, 0, width, height);
    for (var i = 0; i < particles.length; i++) {
        particles[i].attract(mouse.x, mouse.y);
        particles[i].integrate();
        particles[i].draw();
    }
}
{% endhighlight %}