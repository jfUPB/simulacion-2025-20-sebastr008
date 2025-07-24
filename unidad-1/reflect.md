# Unidad 1

## 🤔 Fase: Reflect

### Actividad 9

#### Parte 1

**1 - Diferencia entre random() y noise()**

La principal diferencia entre estas 2 funciones a la hora de observar como se comportan es que la función random genera valores aleatorios sin importar que tan alejados están los valores unos de otros, por ejemplo, esta función puede darte valores tipo 2, 19, 8, 38. 

Por el contrario, la función noise sigue creando valores aleatorios pero estos no son tan alejados unos de otros.

Esto nos puede servir por ejemplo cuando queremos crear animaciones que se vean fluidas y no con un movimiento tan brusco o robótico.

El random podría usarse en obras que no requieran una continuidad en sus trazos, por ejemplo para crear las estrellas de una galaxia, podría usarse esta función.

**2 - Caminata aleatoria vs distribución normal vs distribución uniforme**

La caminata aleatoria al usar la funcion random() suelta valores completamente aleatorios mientras que una distribución normal, la mayoría de datos que arroja se encuentran en la media de los rangos establecidos, aunque esta también cierta cantidad de desviación que hace que no todos los datos sean completamente iguales.

Y la distribución uniforme es en dónde todos los valores tienen exactamente la misma probabilidad de salir.


**3 - Aleatoriedad en el arte generativo**

La aleatoriedad en el arte generativo es bastante fundamental ya que nos permite generar una obra casi que completamente distinta con cada iteración. Podemos tener una obra que genere formas de diferente color según ciertos rangos establecidos (algo parecido a un dado, donde si sale x cara, se genera cierta figura con cierto tamaño y cierto color).

También podríamos generar obras abstractas en dónde la dirección de los trazos sea completamente aleatoria.

**4 - Concepto aleatoriedad actividad 9**

El concepto que más me gustó fue el de ruido Perlin que está de fondo simulando estar en el cielo dentro de una nube. Fue una decisión acertada usar el ruido perlin ya que básicamente lo que estaba haciendo era modificando el valor del brillo de los pixeles aleatoriamente, pero como quería que el cambio se viera de manera fluida, usé el noise() para que los valores no fueran tan diferentes unos de otros, haciendo así que la imagen no cambiara tan bruscamente y logrando el efecto de "nube".

**5 - Definición de "walk" en el contexto de simulación**

Lo entiendo como una manera de representar las decisiones aleatorias que toma un trazo, por ejemplo: si sale 1, das un paso al frente, si sale 2, uno atrás, 3, a la derecha y así, pero la desventaja que tiene este tipo de caminata es que si hacemos un punto por cada paso que de el walker, podremos ver que pasa muchas veces por el mismo lugar. Para solucionar esto, existe el Levy Flight, en dónde básicamente existe una probabilidad de que el walker de un salto considerable de donde esté para explorar nuevos valores.

#### Parte 2

**1 - Dificultades entendiento un concepto**

Las distribuciones normales más que dificultad para entenderlas, es saber en que se podría implementar, no se me ocurren casi ideas en donde pueda usar este tipo de aleatoriedad. De resto siento que comprendí los conceptos enseñados de esta unidad.

**2 - Error que me generó una idea interesante**




