# Unidad 3

## 🤔 Fase: Reflect

### Actividad 11

#### Parte 1:

1. La ecuación vectorial de la segunda ley de Newton es la siguiente: F= m*a en dónde m es la masa del cuerpo y la "a" es la aceleración del cuerpo.

2. Es necesario multiplicar la aceleración por 0 al final de cada update porque necesitamos que en cada frame se calcule una nueva aceleración para que la simulación sea totalmente correcta y acertada. Si no hicieramos esto, la aceleración se mantendría sumando y la simulación se vería rara.

3. En el paso por valor copiamos lo que hay dentro del vector para no modificar directamente lo que se encuentra en este, mientras que paso por referencia modificamos directamente este valor.

4. Que con los algoritmos de aceleración podemos generar resultados "poco realistas" por así decirlo, esto nos puede servir cuando queremos obtener resultados que no esperamos, mientras que cuando modelamos fuerzass, esperamos que pase con nuestro objeto virtual lo que pasaría con un objeto físico real.


#### Parte 2:

1. El concepto la verdad lo tuve claro desde el principio, quería simular las pinturas abstractas que se venden por bastantes millones, pero quería que se hicieran a partir de la aleatoriedad y la atracción de los n cuerpos, así que lo más se me dificultó es hacer que funcionara exactamente como quería mi programa

2. Cuando estaba implementando la función del viento, creo que no se calculó un valor bien y los cuerpos se movían de una manera muy extraña y errática, así que ese fue un momento sorpresa, igualmente el resultado no me gustó así que lo corregí y ahora tiene un viento un poco más realista.

3. Lo veo como una base para empezar a generar arte, podríamos modelar un sistema básico y empezar a modificar las fuerzas para que se comporten de una manera diferente a como se comportaría en el mundo real, pudiendo así generar resultados bastante inesperados e interesantes.

4. Mejoraría mi sistema de N cuerpos implementando la fricción del aire, modificar el campo gravitacional para cada cuerpo, que se vaya cambiando en tiempo real cada x tiempo.



### Actividad 12

#### Claridad del concepto:

Sí, me parece que la obra refleja muy bien el problema de los N-Cuerpos con la ley de Hooke, ya que todos los cuerpos parecen que estan conectados con resortes, quizá sería bueno mostrar en pantalla estos resortes, así también se vería un poco más la influencia de Alexander ya que en sus obras se ven los soportes de los cuerpos pero en general me parece que si cumple con el concepto.

#### Implementación de fuerzas:

Sí, el código implementa correctamente las leyes de Newton: las fuerzas (como las del resorte y el ruido de Perlin) se calculan y luego se aplican a la aceleración de cada cuerpo, que a su vez actualiza la velocidad y posición. Cada loop limpia la aceleración para acumular nuevas fuerzas en el siguiente frame.

#### Creatividad en el modelado:

Claro que si, dependiendo de que tanto tiempo mantengas presionado el mouse y que tanto lo muevas, se generan resultados bastante únicos e inesperados en el movimiento de los cuerpos.

#### Calidad de interacción:

Sí, la interacción está bien diseñada para explorar el sistema de fuerzas: puedes activar o desactivar el Lévy Flight para ver cómo afectan movimientos abruptos y aleatorios, además de interactuar con las partículas usando el mouse para cambiar su tamaño y velocidad

### Actividad 13:

#### Continuar: 

Las 3 actividades propuestas de esta unidad están muy bien planeadas para empezar a entender como funcionan las simulaciones, quizá en la actividad 9 hacer énfasis en no sobre pensar mucho en las 3 actividades para así evitar perder tiempo y enfocarse más en la actividad 10

#### Dejar de hacer:

No, las actividades propuestas me parecieron las necesarias para introducirnos al mundo de fuerzas y simulaciones

#### Progresión conceptual:

Me pareció una progresión natural ya que en la unidad 2 nos introduces el concepto de aceleración y nos invitas a jugar y probar diferentes aceleraciones locas para comprender como esta afecta el movimiento de un cuerpo, luego, en esta unidad, aprendemos a simular sistemas más realistas, profundizando un poco más en el tema de fuerzas y aceleración. Como ya teníamos una idea de como funcionan las aceleraciones en la unidad 2, en esta unidad al meter más fuerzas y conceptos, se nos hace más fácil comprenderlos e implementarlos.

#### Conexión arte-física:

Personalmente soy de los que le gusta generar escenarios poco realistas ya que me gustan los resultados inesperados, así que prefiero usar parámetros muy locos o de lleno ignorar ciertas fuerzas del mundo real para obtener resultados diferentes.

#### Comentario adicional

tqm juan franco nunca cambies, la unidad me gustó bastante
