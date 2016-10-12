## Introducción

"Soy dos con la naturaleza"

—Woody Allen



Nos encontramos aquí: en el inicio. Bueno, casi en el inicio. Si ha pasado un tiempo desde que has escrito cualquier tipo de programación en Processing (o cualquier tipo de matemáticas), esta introducción llevará tu mente de regreso hacia el pensamiento computacional antes de que nos acerquemos a materiales más difíciles y complejos.

En el Capítulo 1, vamos a hablar acerca del concepto de un vector y cómo nos puede servir de manera de un building block para la simulación de movimiento a lo largo del libro. Pero antes de que demos ese paso, pensemos lo que significa que algo simplemente se mueva por la pantalla. Comencemos con uno de las simulaciones de moviemiento más conocidas — El caminante aleatorio.



## I.1 Caminata Aleatoria

Imagina que estás de pie en medio de una barra de equilibrio. Cada diez segundos lanzas una moneda al aire. Cara, das un paso hacia adelante. Sello, das un paso hacia atrás. Esto define una caminata aleatoria —un camino definido por una serie de pasos aleatorios. Llevando la barra de equilibrio hacia el suelo, podrías realizar un paseo aleatorio en dos dimensiones, lanzando la moneda dos veces con los siguients resultados:

| Lanzamiento 1 | Lanzamiento 2 |        Resultado        |
| :-----------: | :-----------: | :---------------------: |
|     Cara      |     Cara      |   Paso hacia adelante   |
|     Cara      |     Sello     |  Paso hacia la derecha  |
|     Sello     |     Cara      | Paso hacia la izquierda |
|     Sello     |     Sello     |    Paso hacia atrás     |



Pareciera que es particularmente un algoritmo poco sofisticado. Sin embargo, las caminata aleatorias pueden ser usadas para modelar fenómenos que ocurren en el mundo real, desde el movimiento de moléculas en un gas, hasta el comportamiento de un apostador a lo largo de un día en el casino. En cuanto a nosotros, empezaremos este libro estudiando un paseo aleatorio con tres metas en mente.

1. Necesitamos repasar un concepto de programación central —Programación Orientada a Objetos. El caminante aleatorio nos servirá como modelo para que el diseño orientado a objetos que usemos haga que cosas se muevan dentro de la ventana de Processing.
2. La caminata aleatoria induce las dos preguntas que nos haremos a través de todo este libro: "¿Cómo definimos las reglas que gobiernan el comportamiento de nuestros objetos?" y luego "¿Cómo implementamos estas reglas en Processing?"
3. A través del libro, periódicamente necesitaremos un entendimiendo básico de aleatoriedad, probabilidad y Perlin Noise. La caminata aleatoria nos permitirá demostrar algunos puntos importantes que nos serán útiles más adelante.


## I.2 La Clase del Caminante Aleatorio

*En Processing se escribe 'class' pero usaremos la palabra clase*

Repasemos un poco la programación orientada a objetos (POO) primero, construyendo un objeto `Caminante`. Esta será solamente un revisión superficial. Si nunca has trabajado con POO antes, y tal vez quieras algo más comprensible; Te sugiero detenerte aquí y revisar los conceptos básicos de Processing en el sitio web antes de continuar.

Un ***objeto*** en Processing es una entidad que tiene datos y funcionalidad. Estamos tratando de diseñar un objeto `Caminante` que a la vez que lleva el seguimiento de sus propios datos (donde existe en la pantalla) y tiene la capacidad de realizar ciertas acciones (así como dibujarse a sí mismo o dar un paso).

Una **class** es la plantilla para construir casos reales de objetos. Piensa en una classe como el cortador de galletas: los objetos son las galletas en sí.

Comenzemos por definir la clase `Caminante` —lo que significa ser un objeto `Caminante`. El `Caminante` sólo necesita dos piezas de datos —un número para su posición en x y uno para su posición en y.

Una clase en lenguaje de Processing lleva el nombre `class`

```javascript
class Caminante {
    int x;			// Objetos tienen datos
  int y;
}
```

Cada clase debe tener un constructor, una función especial que es llamada cuando el objeto se crea por primera vez. Puedes pensarla como el `setup()` del objeto. Ahí, inicializaremos la posición inicial del `Caminante` (en este caso, el centro de la pantalla).

```javascript
Caminante(){    //Objetos tienen un constructor en donde son inicializados.
  x = width/2;
  y = height/2;
}
```

Finalmente, además de los datos, las clases pueden ser definidas con funcionalidad. En este ejemplo, un `Caminante` tiene dos funciones. Primero escribiremos una función que permita al objeto mostraste a sí mismo (como un punto blanco).

```javascript
void mostrar(){  //Objetos tienen funciones.	
  stroke(0);
  point(x,y);
}
```

La segunda función dirige al objeto `Caminante` para que de un paso. Ahora, aquí es donde las cosas se vuelven un poco más interesantes. ¿Recuerdas el piso en donde estábamos dando pasos aleatorios? Bueno, ahora podemos usar una ventana de Processing con esa misma capacidad. En donde hay cuatro posibles pasos. Un paso hacia la derecha puede ser simulado a través del increimente de `x (x++)`; hacia la izquierda con el decremento de `x (x--)`; hacia adelante yendo un pixel hacia abajo `(y++)`; y hacia atrás yendo un pixel hacia arriba `(y--)`. ¿Cómo elegimos desde estas cuatro opciones? Previamente declaramos que podríamos lanzar dos monedas. En Processing, sin embargo, cuando queremos elegir aleatoriamente entre una lista de opciones, podemos elegir un número aleatorio usando `random()`.

```javascript
void paso(){
    int alternativa = int(random(4));  0,1,2, o 3
```

La línea de código superior elige un número de punto flotante entre 0 y 4 y lo conviernte en un entero, con un resultado de 0, 1, 2, o 3. Técnicamente hablando, el número más grande nunca será 4.0, sino que 3.999999999 (con tantos 9 como espacios de decimal existentes); como en el proceso de convertir a un entero se elimina la parte decimal, el `int` más alto que obtendramos será 3. A continuación, tomamos el paso apropiado (izquierda, derecha, arriba, o abajo) dependiendo de cual número aleatorio fue elegido.

```javascript
  if (alternativa == 0) {  //La "alternativa" aleatoria determina nuestro paso.
    x++;
  } else if (alternativa == 1) {
    x--;
  } else if (alternativa == 2) {
    y++;
  } else {
    y--;
  }
}
```

Ahora que hemos escrito la clase, es momento de crear un objeto `Caminante` actual en la parte principal de nuestro dibujo — `setup()` y `draw()`. Asumiento que estamos buscando modelar una caminata aleatoria singular, declaramos una variable globar del tipo `Caminante`.

```javascript
Caminante c;  // Un objeto Caminante
```

Luego creamos el objeto en `setup()` llamando al constructor con el operador `new`.

##### Ejemplo I.1: Caminata aleatoria tradicional

*Cada vez que veas el título Ejemplo en este libro, quiere decir que existe el correspondiente ejemplo en código disponible en GitHub.*

```javascript
void setup(){
  size(640, 360);
  c = new Caminante();  //Crear el Caminante
  background(255);
}
```

Finalmente, durante cada ciclo a través de `draw()`, le pedimos al `Caminante` que de un paso y dibuje un punto.

```javascript
void draw(){
  c.paso();     //Llama a las funciones en el Caminante.
  c.mostrar();
}
```

Ya que solamente dibujamos el fondo una vez en `setup()`, en lugar de limpiarlo continuamente cada vez en `draw()`, vemos el rastro de la caminata aleatoria en nuestra ventana de Processing.



Hay una serie de mejoras que podemos hacer al caminante aleatorio. Por un lado, las alternativas de pasos que tiene el `Caminante` están limitadas a cuatro opciones —arriba, abajo, izquierda, y derecha. Pero, cualquier pixel en la ventana tiene ocho posibles vecinos, y una novena posibilidad es quedarse en el mismo lugar. 

Para implementar un objeto `Caminante` que pueda dar un paso hacia cualquier pixel vecino (o no moverse), podemos elegir un número entre 0 y 8 (nueve posibles alternativas). Sin embargo, una manera más eficiente de escribir el código sería simplemente elegir entre tres posibles pasos en el eje x (-1, 0, o 1) y tres posibles pasos en el eje y.

```java
void paso(){
  int pasox = int(random(3))-1;  //Entrega -1, 0, o 1
  int pasoy = int(random(3))-1;
  x += pasox;
  y += pasoy;
}
```

Llevando esto más adelante, podemos usar números de punto flotante (p.ej. númers decimales) para x e y, y mover de acuerdo a un valor arbitrario aleatorio entre -1 y 1.

```java
void paso() {
  float pasox = random(-1, 1);  //Entrega cualquier número de punto flotante entre -1.0 y 1.0
  float pasoy = random(-1, 1);
  x += pasox;
  y += pasoy;
}
```

Todas estas variaciones en la caminada aleatoria "tradicional" tienen una cosa en común: en cualquier momento en el tiempo, la probabilidad de que el `Caminante` de un paso en una dirección específica es igual a la probabilidad de que el `Caminante` de un paso en cualquier dirección. En otras palabras, si hay cuatro posibles pasos, hay 1 en 4 (o 25%) chances de que el `Caminante` tome una de esos pasos. Con nueve posibles pasos, es 1 en 9 (o 11%) chances.

Convenientemente, así es como la función `random()` funciona. El generador de números aleatorios de Processing (que trabaja detrás de cámaras) produce lo que es conocida como una distribución de numeros "uniforme". Podemos probar esta distribución con un sketch de Processing que cuenta cada vez qye un número aleatorio es elegido y lo grafica como la altura de un rectángulo.

##### Ejemplo I.2: Distribución aleatoria de números

```java
int [] cuentasAleatorias;  // Una lista para seguir la cuenta de cuan seguidos los números aleatorios son elegidos

void setup(){
  size(640, 240);
  cuentasAleatorias = new int[20];
}

void draw(){
  int indice = int(random(cuentasAleatorias.length)); //Toma un número aleatorio e incrementa la cuenta
  cuentasAleatorias[indice]++;
  
  stroke(0);
  fill(175);
  int ancho = width/cuentasAleatorias.length;
  for (int x = 0; x < cuentasAleatorias.length; x++){	// Graficando los resultados
  		rect(x*ancho,height-cuentasAleatorias[x],ancho-1,cuentasAleatorias[x]);
	}
}
```

La captura de pantalla superior muestra los resultados del sketch corriendo por unos minutos. Notemos como cada barra del gráfico difiere en altura. El tamaño de nuestra muestra (p.ej. el número de números aleatorios que hemos elegido) es más bien pequeño y hay algunas discrepancias ocasionales, donde algunos números son elegidos más a menudo. A través del tiempo, con un bueno generador de números aleatorios, esto podría igualar.



##### Números Seudo-Aleatorios

Los números aleatorios que obtenemos de la función `random()` no son verdaderamente aleatorios;  por lo tanto se conocen como "seudo-aleatorios." Son el resultado de una función matemática que simula aleatoriedad. Esta función producirá un patrón a través del tiempo, pero ese período de tiempo es tan largo que para nosotros, ¡es tan bueno como aleatoriedad pura!

##### Ejercicio I.1

Crea un caminante aleatorio con tendendia a moverse hacia abajo y a la derecha. (Veremos la solución en la siguiente sección.)



## I.3 Probabilidad y Distribución No-Uniforme

¿Recuerdas cuando empezaste a programar por primera vez en Processing? Quizás quisiste dibujar muchos círculos en la pantalla. Entonces te dijiste: "Oh, ya sé. Dibujaré todos estos círculos en posiciones aleatorias, con tamaños aleatorios y colores aleatorios." En un sistema de gráficos computacionales a menudo es fácil llenar un sistema con aleatoriedad. En este libro, sin embargo, estamos buscando construir sistemas modelados en lo que vemos en la naturaleza. Dejar todo a la aleatoriedad no es particularmente una solución acertiv a un problema de diseño —en particular, el tipo de problemas que implican crear una simulación orgánica o de aspecto natural.

Con algunos trucos, podemos cambiar la manera en que usamos `random()` para producir distribuciones "no-uniformes" de números aleatorios. Ésto nos será muy útil a través del libro mientras a un número de diferentes escenarios. Cuando examinamos algoritmos genéticos, por ejemplo, necesitaremos una metodología para representar la "selección" —¿cuales miembros de nuesta población deberán ser seleccionados para traspasar su ADN hacia la próxima generación? ¿Recuerdas el concepto de la supervivencia del mejor adaptado? Digamos que tenemos una población de monos evolucionando. No cada mono tendrá las mismas chances de reproducción. Para simular la evolución Darwiniana, no podemos simplemente tomar dos monos aleatoriamente para ser padres. Necesitamos el mejor "adaptado" para ser el más probable de ser elegido. Necesitamos definir la "probabilidad del mejor adaptado." Por ejemplo, un mono particularmente rápido y fuerte tiene un 90% de chance de procreación, mientras que uno débil tiene sólo una chance de 10%.

Hagamos una pausa aquí y miremos los principios básicos de probabilidad. Primero examinémos un único evento de probabilidad, p. ej. la probabilidad que ocurra un dado evento.

Si tienes un sistema de ciertos números como posibles resultados, la probabilidad de que un evento dado ocurra es igual al número de resultados que califican como ese evento, dividido por el número total de todos los posibles resultados. Lanzar una moneda es un ejemplo sencillo —tiene sólo dos posibles resultados, cara o sello. Hay sólo una forma de aparezca cara. La probabilidad de que la moneda resulte como cara, por lo tanto, es uno dividido por dos: 1/5 o 50%.

Toma una baraja de cincuenta y dos cartas. La probabilidad de elegir un as desde el mazo es:

​	**número de aces / número de cartas = 4 / 52 = 0.077 = ~ 8%**

La probabilidad de elegir un diamante es:

​	**número de diamantes/ número de cartas = 13 / 52 = 0.25 = 25%**

Podemos también calcular la probabilidad que multiples eventos ocurran en secuencia. Para hacer esto,  simplemente multiplicamos las probabilidades individuales de cada evento.

La probabilidad de que una moneda de cara tres vecez seguidas es:

​	**(1/2) * (1/2) * (1/2) = 1/8 (o 0.125)**

…quiere decir que una moneda de cada tres veces seguidas es una en ocho (cada "vez" siendo tres lanzamientos).



### Ejercicio I.2

¿Cuál es la probabilidad de que aparezcan dos aces seguidos de una baraja de cincuenta y dos cartas?

Hay un par de maneras en donde podemos usar la función `random()` con probabilidades en código. Una técnica es llenar una lista con una selección de números —con algunos de ellos se repiten— entonces elegir números aleatorios desde esa lista y generar eventos en base a esas opciones.

```java
int[] cosas = new int[5];
cosas[0] = 1;  //1 es almacenado en la lista dos veces, haciendolo más fácil de ser elegido.
cosas[1] = 1;
cosas[2] = 2;
cosas[3] = 3;
cosas[4] = 3;
int indice = int(random(cosas.length)); //Elige un elemento de la lista aleatoriamente
```

Correr este código entregará un 40% de chance de imprimir el valor 1, un 20% de chance de imprimir un 2, y un 40% de chance de imprimir un 3.

También podemos pedir por un número aleatorio (hagámoslo más fácil y sólo consideremos un número aleatorio de punto flotante entre 0 y 1) y permitámosle a un evento que ocurra sólo si nouestro número aleatorio es entre cierto rango. Por ejemplo:

```java
float probabilidad = 0.10; // Una probabilidad de 10%
float r = random(1); // Un valor aleatorio de punto flotante entre 0 y 1
if (r < probabilidad){ // Si nuestro número aleatorio es menor a 0.1, intenta nuevamente!
  // Intenta nuevamente!
}
```

Este método también puede ser aplicado a multiples resultados. Digamos que Resultado A tiene un 60% de chance de ocurrir, Resultado B, un 10% de chance, y Resultado C, un 30% de chance. Podemos implementar esto en código eligiendo un número flotante y viendo dentro del rango que cae.

- entre 0.00 y 0.60 (60%) -> Resultado A
- entre 0.60 y 0.70 (10%) -> Resultado B
- entre 0.70 y 1.00 (30%) -> Resultado C

```java
float numero = random(1);
if (numero < 0.6) { 				// Si el número es menor a 0.6
  println("Resultado A");
} else if (numero < 0.7) {			// Entre 0.6 y 0.7
  println("Resultado B");
} else {							// Mayor que 0.7
  println("Resultado C");
}
```

Podemos usar la metodología superior para crear un Caminante Aleatorio que tienda a moverse hacia la derecha. Aquí hay un enemplo de un `Caminante` con las siguientes probabilidades:

- chance de movimiento hacia arriba: 20%
- chance de movimiento hacia abajo: 20%
- chance de movimiento hacia la izquierda: 20%
- chance de movimiento hacia la derecha: 40%

### Ejemplo I.3 Caminante que tienen a moverse hacia la derecha

```java
 void paso() { 
    float r = random(1);
    if (r < 0.4) {						//Un 40% de chance de que se mueva a la derecha!
      x++;
    } else if (r < 0.6) {
      x--;
    } else if (r < 0.8) {
      y++;
    } else {
      y--;
    }
  }
```

#### Ejercicio I.3

Crea un Caminante Aleatorio con posibilidades dinámicas. Por ejemplo, ¿puedes darle un 50% de chance de movimiento en la dirección de mouse?