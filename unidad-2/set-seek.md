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

### Actividad 6

**Motion 101** El motion 101 básicamente consiste en añadir la velocidad a la posición y después mostrar el objeto a mover en la posición, así en un bucle para formar una animación.

Geométricamente para entenderlo mejor, me imagino a "velocidad" como una flecha por la que "posición" se va a desplazar, ya que constantemente le estamos sumando la velocidad a la posición del objeto que se está mostrando.

**Motion 101 en el ejemplo** En el ejemplo se usa Motion 101 combinado con POO, en dónde se crea una clase Mover donde se crean los vectores de posición y velocidad en el constructor, luego se crea una función para modificar la posicion de este objeto llamada update, luego en la función show, se encarga de mostrar en este caso un circulo con la posición nueva dada por la velocidad. Aquí se puede notar el motion 101 porque usa los vectores posición y velocidad de la manera en la que expliqué anteriormente.

### Actividad 7

Pude observar como con cada aceleración tenía un comportamiento diferente, con la aceleración constante, la pelota siempre iba agarrando más y más velocidad, pero al limitarla esta llegaba al tope y seguía con esa velocidad, con la velocidad aleatoria, la pelota tomaba cambios de dirección repentinos, haciendo que la pelota se mueva de manera "errática", y con la aceleración por el mouse, la pelota sigue por donde va el mouse.

Pude experimentar bastante con todas estas aceleraciones y llegue a este codiguito:

```js

let mover;

function setup() {
  
  mover = new Mover();
  createCanvas(400, 400);
}

function draw() {
  background(220);
  
  mover.update();
  mover.show();
  mover.checkEdges();
  
  
}

class Mover{
  
 constructor(){
   
   this.position=createVector(0,0);
   this.velocity=createVector(0,0);
   
   this.accel=createVector(0.01,0.2)

   
 }
  
  update(){
    
    let mouse = createVector(mouseX,mouseY);
    
    let dir = p5.Vector.sub(mouse, this.position);
    dir.normalize();
    dir.mult(0.2);
    
    this.accel = dir;

    this.velocity.add(this.accel);
    
    
    this.position.add(this.velocity);
    this.velocity.limit(15);
    
    console.log(this.velocity.toString());
  }
  
  show(){
    
    fill(255);
    circle(this.position.x,this.position.y,70);
  }
  
  checkEdges(){
    if(this.position.x>width || this.position.x<0 ){
      this.velocity.x *= -1;
      this.velocity.mult(0.8);
    }
    
    
    if(this.position.y>height || this.position.y<0 ){
      this.velocity.y *= -1;
      this.velocity.mult(0.8);
      
    }
    
  }
  
  
}
```









