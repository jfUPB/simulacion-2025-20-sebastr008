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


### Actividad 4

**magSq():** Este método calcula la magnitud del vector usando sus componentes x,y y lo eleva al cuadrado 

**mag():** Este método eleva al cuadrado esta magnitud, haciendola más eficiente ya que el procesador no tiene que computar la raíz cuadrada

**normalize():** Este método escala los componentes de un vector dado para que su magnitud sea 1, haciendo así que sea más fácil controlar direcciones o fuerzas en simulaciones o animaciones

**dot():** Sirve para realizar el producto punto de 2 vectoes y obtener un escalar. 

Como tal no importa si se usa el método por referencia o estático, simplemente cambia la manera de sintaxis a la hora de hacer el producto punto.


Referencia:

```js
let direccion = createVector(1, 0);
let objetivo = createVector(3, 2);

let alineamiento = direccion.dot(objetivo);
```

Estático:

```js
let fuerza = createVector(2, 3);
let aceleracion = createVector(4, 1);

let resultado = p5.Vector.dot(fuerza, aceleracion);
```

**Interpretación de un producto cruz:** 

Si tenemos 2 vectoes A y B estos formarán un paralelogramo, si hacemos un producto cruz entre estos 2 vectores, obtendremos un tercer vector que será perpendicular al paralelogramo formado por A y B, (si cambiamos el orden por ejemplo B x A, el 3er vector tendra su dirección invertida).

La magnitud de este tercer vector será igual al área del paralelogramo formado por los vectores A y B pero si el ángulo que hay entre A y B es 0° o 180°, no existe un paralelogramo, por lo tanto, el área es 0, o no existe.

**dist():** Este método sirve para calcular la distancia entre 2 puntos ya sean de coordenadas o vectores. Puede servir para dibujar lineas entre estos 2 puntos, detectar proximidad de algun elemento o crear eventos si se cumple x distancia entre varios puntos.

**limit();** Sirve para colocarle un máximo o un mínimo a la magnitus de un vector, esto es útil si por ejemplo estamos haciendo algo con velocidades o aceleraciones, para limitar estas y que el programa no se rompa (si es que muchos de los cálculos dependen de estos vectores).

### Actividad 5

**Código en p5.js**

```js
let mov = 0;
let direccion = 1;

function setup() {
    createCanvas(600, 600);
}

function draw() {
    background(200);

    let v0 = createVector(50, 50);
    let v1 = createVector(250, 0);
    let v2 = createVector(0, 250);
    let v3 = p5.Vector.lerp(v1, v2, mov);
    let v4 = createVector(v2.y+v0.y,v0.x);
    let v5 = createVector(-v1.x, v2.y);
  
    let azulito = color(0,0,255);
    let rojito = color(255,0,0);
    let blending = lerpColor(rojito,azulito,mov);
    
    
    drawArrow(v0, v1, 'red');
    drawArrow(v0, v2, 'blue');
    drawArrow(v0, v3, blending);
    drawArrow(v4, v5, 'green');
  
    mov += 0.01*direccion;
  
  if (mov<=0 ){
    direccion = 1;
  }
  
  if (mov>=1){
    direccion = -1;
  }

}

function drawArrow(base, vec, myColor) {
    push();
    stroke(myColor);
    strokeWeight(3);
    fill(myColor);
    translate(base.x, base.y);
    line(0, 0, vec.x, vec.y);
    rotate(vec.heading());
    let arrowSize = 7;
    translate(vec.mag() - arrowSize, 0);
    triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
    pop();
}
```

**Método lerp**

El método lerp crea nuevos componentes x,y que están a la misma distancia entre 2 vectores, tiene un parámetro (amount) que sirve para definir que tan cerca esos componentes están de un vector o de otro (si está cerca al 0, sus componentes estarán más cerca al primer vector, si está más cerca al 1 sus componentes estarán más cercanos al nuevo vector).

**Método lerpColor**

Este método es exactamente lo mismo que lerp, solo que en vez de usar componentes de vectores, usa valores rgb, tiene 3 parametros, el primero es el color del que parte, el segundo es el color hacia donde va, y el 3ro es el mismo que el amount en lerp, dependiendo de este número, el color será más parecido a uno o a otro.






