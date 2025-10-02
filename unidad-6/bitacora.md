# Evidencias de la unidad 6

## Actividad #1


<img width="742" height="743" alt="image" src="https://github.com/user-attachments/assets/cf2e4387-d0e8-414e-b503-203c85c106fc" />

 


Me llamó la atención esta imagen principalmente porque nunca había visto una generación de trazos tan partícular, usualmente se ven trazos con curvas bastante redondeadas, y transiciones en su cambio de dirección bastante suaves, aparte, no todos los trazos tienen el mismo grosor, unos son bastante gruesos y otros bastante finos, normalmente veo trazos bastante uniformes respecto a su grosor y también con cambios de dirección más "curveados", por eso me llamó bastante la atención esta obra, aparte de que me gustó bastante el resultado final.

---

<img width="907" height="914" alt="image" src="https://github.com/user-attachments/assets/192c7126-8999-4dd8-9476-2df70a103602" />

Lo que más me llamó la atención de esta obra sin embargo es el resultado final, los colores y las formas son bastante bonitas; también me llamó bastante la atención y me llenó de curiosidad como hizo el campo de flujo para generar esta imagen tan partícular, parecen paraguas que repelen la lluvia o incluso medusas. Un resultado bastante interesante.


## Actividad 2:

**¿Qué es una fuerza de dirección?**

La steering force es la corrección que se le aplica a la velocidad de un objeto para que se acerque a una velocidad deseada. Primero se define un vector de deseo que apunta al objetivo, cuya magnitud no supera la velocidad máxima que puede llefar el objeto. Luego se calcula la dirección restando el vector de deseo con la velocidad y para que no sea tan brusco el movimiento se limita este vector, así el objeto no salta ni cambia bruscamente, sino que gira y ajusta su trayectoria de manera más suave.

**¿Qué diferencia tiene este tipo de fuerza con las que ya hemos estudiado en el contexto de la simulación de agentes?**

Antes se trabajaban con fuerzas del entorno como la gravedad, el viento o el rozamiento. Esas fuerzas empujan a los objetos sin intención y afectan por igual a todos los objetos cuando se crean. La steering force es distinta porque nace del objetivo del agente, este le indica hacia dónde quiere ir y cuánto debe corregir su velocidad.

**¿Qué relación tiene la steering force con Craig Reynolds y su trabajo en simulación de comportamiento animal?**

La relación que vi es que el movimiento colectivo puede verse natural si cada agente aplica tres comportamientos locales. Separación para no chocar. Alineación para apuntar en direcciones parecidas. Cohesión para mantenerse cerca. Cada uno genera una steering force, se les da pesos, las fuerzas se suman, se "recorta" el resultado para no pasar los límites de fuerza y de velocidad y se les aplica esa fuerza como aceleración en cada frame y de ahí aparece un movimiento que se siente bastante natural

## Actividad 3:

**Explica brevemente la estructura de datos usada para el campo de flujo y cómo se generan sus vectores.**

El flowfield se almacena en un array 2D llamado “field” que cubre todo el lienzo en celdas del tamaño que marque la “resolution”. En cada casilla guardo un vector 2D. Ese vector sale de un ángulo generado con ruido suave en las coordenadas de la celda y lo paso a dirección con coseno y seno (o p5.Vector.fromAngle), si quiero que el campo cambie en el tiempo, avanzo un “z” en el ruido y los vectores se actualizan de forma pareja frame a frame.

**Describe con tus palabras cómo un agente utiliza el campo para calcular su fuerza de dirección**

En cada update() el agente pregunta qué vector le toca con flow.lookup(this.position), lo ajusta a su velocidad objetivo con desired.setMag(this.maxspeed) y arma la fuerza de giro como la diferencia entre ese “desired” y su velocidad actual. A esa fuerza le pongo un tope con this.maxforce para evitar volantazos y recién ahí la aplico. Después integro normal: acumulo aceleración, actualizo la velocidad, muevo la posición y limpio la aceleración para el siguiente ciclo.

**Lista los parámetros clave identificados (resolución, maxspeed, maxforce)**

- resolution: define cuántas columnas/filas tiene el “field” y el nivel de detalle del flujo.

- noiseScale / inc: escala del ruido usado para generar el ángulo por celda.

- zOffset / zSpeed: sirve para que el campo “se anime” de forma suave

- maxspeed (agente): velocidad objetivo a la que alineo el vector “desired” que viene del campo.

- maxforce (agente): tope de la fuerza de giro, controla qué tan rápido puede corregir rumbo sin volantazos.

- lookup (modo de lectura): cómo se lee el vector de la malla en una posición

- vehicleCount: cantidad de agentes

**Modificación**

La modificación que le hice a la forma en la que se generan los vectores de campo de flujo consiste en restringir la dirección que puede tomar cada vector a 8 direcciones posibles, como la matemática no es mi mayor fuerte, le explique a chatgpt como funcionaba la función "init()" de la clase "flowfield" y le dije que quería que me restringiera el ángulo posible de cada vector a 8 posibles direcciones. En “setup()” también cambié la resolution de 20 a 8 para que el campo tenga muchas más celdas en pantalla y esos cambios se vean más seguido. Por último, en el constructor de “Vehicle” baje considerablemente el random de “maxspeed” y “maxforce” lo aumenté para que el agente se mueva mucho más lento pero tenga fuerza suficiente para cambiar su dirección de una cuando pisa una celda con dirección distinta, haciendo así que las trayectorias dejen de ser tan suaves y aparecen quiebres visibles cuando cruzan de celda, también se forman como carriles y los grupos tienden a alinearse en las mismas direcciones.

<img width="629" height="228" alt="image" src="https://github.com/user-attachments/assets/beab2211-bdec-4332-8abe-04f43dbd61ae" />


Codigo que alteré:

```js
createCanvas(640, 240);
  // Make a new flow field with "resolution" of 16
  flowfield = new FlowField(8);
  // Make a whole bunch of vehicles with random maxspeed and maxforce values
  for (let i = 0; i < 120; i++) {
    vehicles.push(
      new Vehicle(random(width), random(height), random(.5, 2), random(0.5, 0.10))
    );
  }
}
```

```js
 init() {
    // Reseed noise for a new flow field each time
    noiseSeed(random(10000));
    let xoff = 0;
    let inc = 0.35;                 
    let sector = TWO_PI / 8.0;      
    for (let i = 0; i < this.cols; i++) {
      let yoff = 0;
      for (let j = 0; j < this.rows; j++) {
        //{.code-wide} In this example, use Perlin noise to create the vectors.
        let angle = map(noise(xoff, yoff), 0, 1, 0, TWO_PI);
        angle = floor(angle / sector) * sector + sector * 0.5;
        this.field[i][j] = p5.Vector.fromAngle(angle);
        
        yoff += inc;
      }
      xoff += inc;
    }
  }
```

```js
flowfield = new FlowField(8);
```














