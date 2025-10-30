# Evidencias de la unidad 8

## Actividad 1

Vi fragmentos de Sonar+D (Carles Viarnès + Alba G. Corral) y del show con Le Parody en Zaragoza. En Sonar+D, el piano y las capas ambientales empujan paisajes de líneas y manchas que se densifican cuando sube la intensidad; los graves abren “respiraciones” lentas (zoom/scale y desplazamientos suaves), mientras los agudos dibujan filamentos finos y destellos cortos, muy atados al ataque de cada nota. En Le Parody, como la base es más rítmica y vocal, los visuales reaccionan con cortes más nítidos: cada kick empuja una expansión de partículas/feedback y la voz modula contornos y bordes (umbral/luma) que aparecen y desaparecen con el envelope. En ambos casos se siente una lectura de ritmo (onsets), energía (RMS) y espectro (FFT por bandas) que gobierna forma, densidad y paleta.

Lo más “generativo” que noté: partículas guiadas por campos de flujo (ruido simplex/perlin para dirección), deformaciones sobre mallas/planos usando ruido animado, feedback con acumulación y pequeñas variaciones de semilla, y paletas que cambian por estados, no por cortes de video. Esas reglas producen patrones que evolucionan solos; aunque la música fuera la misma, las microdiferencias del audio en vivo y los tweaks manuales (thresholds, ganancia de bandas, offsets de tiempo, random seeds) hacen que el sistema tome rutas distintas cada vez. No hay loops cerrados, hay sistemas con condiciones iniciales que nunca son idénticas, por eso cada visualización termina siendo única.

Sobre la “liveness”: se siente que todo está respirando con la música. No es un render disparado, es una conversación: si sube el bombo, el cuadro responde; si la artista cambia el fraseo, la textura lo acusa. Esa fragilidad controlada (el riesgo de que algo se desborde o se quede corto) transmite presencia. Yo lo leo como performatividad real: decisiones de parámetros en tiempo real, sensibilidad a los transitorios y a la dinámica del momento. Eso genera cercanía y también tensión rica: lo que vi existe sólo en ese ratito y no vuelve igual.

## Actividad 2:

**1. La pieza musical elegida** : La canción se llama [Ramalama (Bang Bang)](https://www.youtube.com/watch?v=YuGe533mWiA&t=4s), es una canción que escogimos porque sentimos que tenía mucha personalidad y además nos daba muchas vibes de [Halloween/brujas](https://www.youtube.com/watch?v=9RNQ_kl-gBk&list=RD9RNQ_kl-gBk&start_radio=1) , puedes clic al link para comprender mejor las sensaciones de la canción ya que la artista tiene una puesta en escena que visualmente representa muy bien a la pista sonora y queriamos lograr algo muy similar pero con cosas abstractas y simbólicas, así que nos pareció perfecta para la ocasión (estamos en el MOOD). 

**2. La descripción de tu concepto visual** : visualmente lo teniamos claro: representar un **Aquelarre de Brujas**. Los conceptos clave para las visuales era una congregación de objetos, movimientos en circulos para dar sensación de que algo está pasando entre esas formas/figuras. Finalmente integrar un resultado del Aquelarre: la hechicería, un conjuro, una maldición o algo así. 

**3. Los inputs seleccionados** :  

- La música, es uno de los principales modificadores de las formas (colores, tamaños y movimientos) que aparte de ser un requisito para esta entrega, es una manera de dinamizar los visuales ya que la musica tiene diferentes momentos que se pueden transmitir como input. 
- Modelo MediaPipe de visión artificial, decidimos integrarlo para darle más dinámica al papel de VJ, estamos convencidos de que esta técnica puede ser muy revolucionaria y decidimos utilizarla en conjunto de la materia de simulación para hacer esa convergencia entre ambas materias, que a nuestro parecer se complementan muy bien.


**4. ¿Qué algoritmos o técnicas planeas usar (ej: flow fields, flocking, física, partículas, etc.) y por qué?**

Para este proyecto utilizamos la física integrada al movimientos de nuestras formas y elementos de los visuales y además un sistema de particulas para representar esos hechizos/conjuros que serán el resultado del aquelarre, nos parece perfecto este efecto de "particulas flotantes" para dar esa sensación de MAGIA y fantasia. 

<img width="1128" height="362" alt="image" src="https://github.com/user-attachments/assets/18712e28-b1be-4585-8962-3a789708142e" />


## Actividad 3

Para hacer nuestro APPLY hicimos una asociación entre Sebas Torres y Manu Buriticá, con el objetivo de utilizar un nuevo programa que desconociamos llamado TouchDesigner pero con el que definitivamente queriamos experimentar. 

Para empezar hicimos un proceso de ideación en cuanto  a los efectos que queríamos hacer para llegar al concepto visual que creamos en conjunto.

Luego, empezamos nuestro proceso de experimentación/replicación de tutoriales que ya existen en YouTube con el fin de comprender cómo utilizar el programa y dominarlo más. Despues de esa indagación y práctica de manejos básicos de Touch iniciamos la experimentación que nos llevó a resultados muy diferentes y únicos. 

Seguido a esto, empezamos a ver documentación y tutoriales acerca de visuales Audioreactivos que nos ayudarian luego para implementarlo en nuestro modelo, finalmente lo hicimos y pasamos al modelo MediaPipe que era importante para nosotros con el fin de agregar interactividad a este proyecto. 

**Grabación de toque en tiempo real**

![dfb21500-8183-400e-8a0a-4bb11eb1a8f0](https://github.com/user-attachments/assets/102de468-556a-457d-ab05-ecb23a71f9b9)


## AUTOEVALUCIÓN

Nota: 5. Consideramos que nos merecemos un cinco porque con Manu trabajamos en tiempo record para lograr este resultado, hicimos un trabajo de exploración y además aprendimos mucho sobre el programa que estamos usando en este proceso de creación. Así cómo también integramos un modelo de visión artificial para tener una interacción más dinámica.

