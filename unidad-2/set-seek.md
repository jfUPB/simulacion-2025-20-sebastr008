# Unidad 2

## 🔎 Fase: Set + Seek

### Actividad #1

La suma de 2 vectores en p5.js funciona justo igual que en la vida real, sumándolos componente a componente.

position = position + velocity; no funciona ya que el signo "+" no está sobrecargado, haciendo que no sea posible así la suma de vectores, para sumar 2 vectores debemos usar la función nombreVector.add(vector2);

### Actividad #2

Simplemente tuve que crear 3 vectores, uno de aceleración que randomiza los valores que tendra la velocidad, luego uno para la posicion y la velocidad. 

```js
let position;
let velocity;
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
  accel = createVector(floor(random(-10,10)),floor(random(-10,10)))
  velocity = createVector(accel.x, accel.y);
}

class Walker {
  constructor() {
  position = createVector(100, 100);
  }

  show() {
    stroke(0);
    point(position);
  }

  step() {
    
    position.add(velocity);
    
  }
}
```

### Actividad #3

Primeramente debemos de corregir el nombre de la variable en todos los sitios que se utiliza, luego espero obtener los resultados: 6,9,0 20,30,0

Una vez corrí el código efectivamente obtuve los 2 vectores mencionados anteriormente.

Un ejemplo que hicimos en clase fue el de copiar el vector para no modificar el vector directamente:

```js
let position;

function setup() {
    createCanvas(400, 400);
    position = createVector(6,9);
    console.log(position.toString());
    playingVector(position);
    console.log(position.toString());
    noLoop();
}

function playingVector(v){
    let pos = v.copy();
    pos.x = 20;
    pos.y = 30;
}

function draw() {
    background(220);
    console.log("Only once");
}
```
Como copiamos lo que habia en la dirección "v" que es la misma que "position" en otro vector diferente y lo que modificamos fue ese otro vector diferente, el vector original no se modifica y se imprime 2 veces el vector 6,9,0

Si ponemos un console.log que muestre el contenido de pos, ahí si nos mostrará 20,30,0

Con esto aprendí a no modificar vectores directamente y repasé un poco el concepto de referencias en Javascript
