# //705

## I.6 Perlin Noise (Un acercamiento mas fluido)

Un buen generador de números aleatorios, produce valores que no tienen relación entre si y no muestran patrones detectables.Como hemos comenzado a ver, un poco de aleatoriedad puede ser un buen elemento cuando se quiere lograr programación orgánica, crear comportamientos mas real. Sin embargo, la aleatoriedad como principio no es necesariamente natural. Un algoritmo conocido como "Perlin noise", nombrado así por su inventor Ken Perlin, toma este concepto en cuenta. Perlin desarrollo la función de noise -ruido- mientras trabajaba en la película Tron a principios de la década de 1980;  fue diseñado para crear texturas para efectos generados por computador. En 1997 Ken Perlin ganó un Premio de la Academiapor los logros técnicos de su trabajo. Perlin noise puede ser usado para generar varios efectos con atributos naturales, como nubes, paisajes y patrones texturizados como el mármol.

Perlin noise tiene una apariencia mas orgánica porque produce una secuencia de números seudo aleatorios, ordenados naturalmente. El gráfico que se ve a la izquierda muestra el Perlin noise a través del tiempo,con el eje x representando el tiempo; observa la fluidez de la curva. El gráfico de la derecha exhibe aleatoriedad pura de números durante el tiempo. (El código para generar estos gráficos esta disponible en el libro adjunto en las descargas). 

/imagen/

Processing tiene un implemento construido de el algoritmo Perlin noise: la función `noise()`. Esta función toma una, dos o tres argumentos, dependiendo de si noise es calculado en una, dos o tres dimensiones. Comencemos por observando noise unidimensional.

-------------------------------------------------------------------------------------------------------------

### Noise Detail

La referencia nosie de Processing dice que esta es calculada sobre varias "octavas".LLamar al función `noiseDetail()` va a cambiar el numero de la `octava` y su importancia relativa entre una y otra.  Esto a su vez va a cambiar como la función noise se comporta.

Para aprender mas sobre como funciona noise, Ken Perlin ofrece una conferencia en línea donde explica más el tema. 

-----

Dibuja un circulo en la ventana de Processing, con laposición de x aleatoria.

```java
float x = random(0,width);                 						 Locación aleatoria de x
ellipse(x,180,16,16);
```

Ahora, en vez de una locación aleatoria de x, queremos una locación de el eje x generado por Perlin noise, que es mas suave. Probablemente pensaras que lo único que se necesita es reemplazar `random()` por `noise()`, p. ej.

```java
float x = noise(0,width);			                            	Locación x de noise?
```

 **linea de cap 747***

Conceptualmente, esto es exactamente lo que queremos hacer, calcular el valor de x entre 0 y width -ancho-  acorde a Perlin noise, pero esta no es la forma de implementarlo. Mientras el argumento para la función`random()` especifica un rango de valores entre un valor mínimo y maximo, `noise()` no funciona de esta manera. En cambio, el rango de resultados es arreglado, siempre retorna a valores entre 0 y 1. Veremos en un momento como podemos abordarlo de manera mas simple con las función `map()` , pero primero vamos a examinar los argumentos que `noise()` espera que entreguemos. 

Podemos pensar de el Perlin noise uni-dimensional como una secuencia de valores en el tiempo. Por ejemplo:

| Tiempo | Valor de noise |
| :----: | :------------: |
|   0    |     0.365      |
|   1    |     0.363      |
|   2    |     0.363      |
|   3    |     0.364      |
|   4    |     0.366      |

Ahora, para poder acceder a un valor de noise en particular, debemos expresar un "momento especifico del tiempo" a la función `noise()`. Por ejemplo:

```java
float n = noise(3);
```

De acuerdo con la tabla de arriba, `noise(3)` volverá  a 0.364 igual al  tiempo 3.  Podríamos mejorar esto usando una variable para Tiempo y pidiendo una variable constantemente en `draw()`. 

```java
float t = 3; 

void draw() {
  float n = noise(t);                        Necesitamos el valor de noise para un  
  println(n);                                         momento especifico de Tiempo.
  }
```

El código de arriba resulta en el mismo valor impreso una y otra vez. Esto ocurre porque estamos pidiendo a la función `noise()`, el resultado del mismo punto en el tiempo -3- una y otra vez.   Si aumentamos la variable de tiempo `t`, obtendrémos diferentes resultados. 

```java
float t = 0;                     				 Normalmente comenzaremos al        tiempo = 0,  									 pero esto es arbitrario. 
 void draw() {
  float n = noise(t);
  println(n);
  t += 0.01;                  					  Ahora, avanzamos en el tiempo! 
}
```

Que tan rápido es el incremento de `t` tambien afecta la fluidez de noise. Si hacemos salto largos en el tiempo, entonces estamos saltando adelante y los valores seran más aleatorios. 

/imagen fig I.7/

Trata de ejecutar el código varias veces, aumentando `t` por 0.01, 0.02, 0.05, 0.1, 0.0001, y podrás ver diferentes resultados. 



### Mapping Noise 

Ahora estamos listos para respoder la pregunta de que hacer con los valores de noise. Una vez que tenemos el valor con un rango entre 0 y 1, depende de nosotros mapear  ese rango, al que queremos.  La forma mas fácil de hacer esto es con la función de processing `map()`.  Esta función se compone de cinco argumentos. Primero va el valor que queremos mapear, en este caso `n`.  Luego debemos darle los valores del rango que tenemos, seguido por los valores del rango que deseamos. 

/fig I.8/

En este caso, sabemos que noise tiene un rango que va 0 a 1, sin embargo, queremos dibujar un cirlculo con un rango que se extienda entre  0 y el ancho -width-  de la ventana. 

```java
float t = 0;

void draw() {
  float n = noise(t);
  float x = map(n,0,1,,width);                Uso de map() para personalizar el rango de   ellipse(x,180,16,16);			 		   Perlin noise. 
    
  t+= 0.01;
}
```

Podemos aplicar la misma lógica a nuestro caminante aleatorio, y asignar los valores de su eje x y eje y según Perlin noise.

/ejemplo random walker con perlin noise/



#### Example I.5: Caminante con Perlin noise

```java
class caminante{
  float x,y;
  
  float tx,ty;
  
  caminante(){
  tx = 0;
  ty = 10000;    
}
  void paso(){
 	 x = map(noise(tx)),0,1,0,width);		  Locación de x e y mapeadas desde noise.
	 y = map(noise(ty)),0,1,0,height);
  
 	 tx += 0.01;								  Avanzar por el "tiempo".	      ty += 0.01;
	}
  
}
```



Notemos como en el ejemplo anterior se requiere un par de variables adicionales: `tx` y `ty`. Esto se debe a que debemos seguir la cuenta de dos variables de tiempo, una para la posición de x, y otra para la posición de y, del objeto `caminante` . Pero estas varibles tienen una peculiaridad. ¿Cuál es la razón de que `tx` empiece en 0 y que `ty` empiece en 10.000? Mientras estos números son elegidos arbitrariamente, hemos específicamente inicializado nuestras dos variables de tiempo, con diferentes valores. Esto es porque la función noise es determinista: nos entrega el mismo resultado para un tiempo `t` específico, una y otra vez.  ~~Si pidieramos el valor de noise al mismo tiempo~~ `t` para `x` e `y` , ambas serian iguales; es decir, que el objeto `caminante` se movería en dirección diagonal. En cambio, simplemente usamos diferentes sectores de la dimensión de noise, partiendo en 0 para `x` , y en 10.000 para  `y` , de modo de que `x` e `y` puedan aparentar actuar de forma independiente.

/figura I.9/

En realidad, no existe una concepción del paso del tiempo aquí. Es una metáfora que nos permite entender como trabaja la función noise, pero realmente lo que tenemos es espacio, antes que tiempo. El gráfico de arriba muestra una secuencia linear de valores de noise en un espacio unidimensional, y podemos pedir un valor en una posición `x` especifica cuando queramos. Por ejemplo, a menudo veras variables llamadas xoff, para indicar el salto que se suma a `x` a lo largo del gráfico de noise,  antes que `t` para tiempo ( como se indica en el diagráma)

------

##### Ejercicio I.7

En el caminante aleatorio el resultado de la función esta mapeado directamente a la posición de el `caminante()`.  Crea un caminante aleatorio donde el resultado de la función `noise ()`  sea mapeado para determinar el tamaño del paso de `Walker` .

------



### Noise bidimensional

La noción de que los valores de noise estan en un espacio unidimensional, es relevante, ya que nos conduce a la discución de una dimensión bidimensional. Vamos a reflexionar sobre esto un momento. Con noise en una dimensión tenemos una secuencia de valores en la que cualquier valor entregado es similar al anterior y al siguiente. Ya que el valor esta en una dimensión, este tiene solo dos vecinos: un valor que viene antes (a la izquieda del gráfico) y el que viene después (a la derecha).

/fig. I.10 :1D noise/    /fig. I.11: 2D noise/

Noise bidimensional funciona exactamente de la misma forma, conceptualmente. La diferencia es que no estamos buscando valores a lo largo de una trayectoria lineal, sino, valores que se están en una grilla. Piensa en un pedazo de papel cuadriculado con números en cada celda. Un valor dado será similar a todo los valores colindantes : arriba, abajo, a la derecha, a la izquiera, y a lo largo en cualquier diagonal.

Si se visualizara este papel cuadriculado con cada valor asignado a la luminosidad de un color, obtendríamos algo que parece una nube. El blanco se encuentra junto al gris claro, el cual esta junto al gris, el cual esta junto al gris oscuro, que esta junto al negro, que esta al lado del gris oscuro, etc. 



/imagen noise 2D/



Este es el por qué noise fue originalmente inventado. Los parámetros pueden ser ajustados o los colores alterados, para hacer que la imagen resultante tenga un aspecto más parecido al mármol o a la madera, o a cualquier otra textura orgánica. 

Vamos a echar un vistazo rápido a la forma de implementar noise bidimensional en Processing.  Si quisieramos colorear cada pixel de una ventana aleatoriamente, necesitariamos un bucle anidado, uno que  acceda a cada pixel y  recoja un valor de brillo al azar. 

```java
loadPixels();
for (int x = 0; x < width; x++) {
  for (int y = 0; y < height; y++) {
//Brillo dado por random()!
    float bright = random(255);
    pixels[x+y*width] = color(bright);
  }
}
updatePixels();
```



Para colorear cada pixel de acuerdo a función `noise()` , haremos exactamente lo mismo, solo que en vez de llamar la función `random()`, llamaremos a `noise()`.  

```java
 float bright = map(noise(x,y),0,1,0,255);    //Brillo dado por Perlin noise()!
```

Conceptualmente este es un buen comienzo - te entrega un valor para cada posición`(x, y)` en nuestro espacio bidimensional. El problema es que esto no tendrá la calidad nubosa que queremos. Saltar de el pixol 200 al 201 es un salto demasiado largo para noise. Recuerda, cuando trabajo con noise unidimensional, aumentamos nuestra variable de tiempo en 0.01 en cada cuadro, no en 1! Un buena solución para este problema es usar una variable diferente para los argumentos de noise. Por ejempli, podríamos incrementar una variable llamada `xoff` cada vez que se mueva horizontalmente, y una variable `yoff` para cuando se mueva en sentido vertical a tráves de el bucle anidado. 

------



# <*linea 966* >

### Ejemplo I.6: 2D Perlin noise

------

```java
float xoff = 0.0;		//Iniciar xoff en 0
 
for (int x = 0; x < width; x++) {
//Por cada xoff, inicia un yoff en 0
  float yoff = 0.0;
 
  for (int y = 0; y < height; y++) {
//Usa xoff ayyoff para noise().
    float bright = map(noise(xoff,yoff),0,1,0,255);
//Usa x para y para la pisicion de los pixeles
    pixels[x+y*width] = color(bright);
//Aumentar yoff.
    yoff += 0.01;
  }
//Aumentar xoff.
  xoff += 0.01;
}
```

#### Ejercicio I.8

Juega con el color, `noiseDetail()`, y el ritmo en el que `xoff` y `yoff` son aumentados para lograr diferentes efectos visuales. 



#### Ejercicio I.9

Agrega a noise un tercer argumento que aumente uno por ciclo por `draw()` para animar el noise bidimensional. 



#### Ejercicio I.10

Usa los valores de noise para hacer elevaciones de topograficas. Usa como referente Mira el pantallazo de abajo como referente.

/imagen noiseLandscape/



En esta sección hemos revisado varias maneras tradicionales de usar Perlin noise. Con noise unidimensional creamos la ilusión de un objeto ambulante, usando valores suaves para aignar la posición  `x` e `y` de este.  En cambio con noise bidimensional creamos un patrón nuboso con valores suaves en un plano de pixeles.  Es importante recordar, que sin embargo, los valores de Perlin noise son solo eso -valores. No estan amarrados a posiciones de pixeles o a colores. Cualquier ejemplo en este libro que contenga variables puede ser controlado a través de Perlin noise. Por medio de Perlin noise podemos modelar la fuerza del viento. Así mismo, se puede controlar el ángulo entre las ramas de un arbol fractal, o la velocidad y dirección  de objetos moviendose en una grilla que simula una corriente.  

/fig I.12: tree with perlin noise/ /fig I.13: campo de flujo con perlin noise/

# //1009//