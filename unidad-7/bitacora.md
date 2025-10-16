# Evidencias de la unidad 7

## Actividad 1

**Ejemplos**

<img width="383" height="383" alt="image" src="https://github.com/user-attachments/assets/4ca715db-78ab-41d5-8f0e-cce9176918a3" />

Se puede ver como en esta imagen representa la ciudad de Manhattan alargando las letras más altas para así dar la ilusión de que son edificios, y como en aquella ciudad hay tantos y son tan altos, es la representación perfecta de esta palabra.

<img width="384" height="378" alt="image" src="https://github.com/user-attachments/assets/87efea29-5b0b-4089-8491-aeddef2e5856" />

Al oscurecer el fondo y dejar solo un poco las letras visibles pero, dejando en blanco las bolitas de las letras "i" representan los ojos abiertos de una persona que no es capaz de dormir, el fondo oscuro es la habitación con la luz apagada, y las letras se dejan grises para que se pueda distinguir un poco del fondo sin que se pierda el significado de lo que se quiere transmitir.

<img width="379" height="383" alt="image" src="https://github.com/user-attachments/assets/d12fde67-2bc9-4405-9a9f-8fc95462554c" />

Con solo inclinar la palabra y rellenar cierta parte de las letras con otro color ya es suficiente para dar un significado bastante grande, me parece teso por parte del artista como con cambios tan mínimos es capaz de añadir mucho más significado del que ya tiene a una palabra.

<img width="385" height="384" alt="image" src="https://github.com/user-attachments/assets/87237744-bffc-447a-826e-dfb49877e188" />

Me gustó mucho la transición que hace el fondo, como se va oscureciendo todo para que al final el mismo eclipse sea el encargado de formar la "c" de la palabra.


**Mis propias ideas**

Para la palabra "Loop" estaba pensando en hacer que las 2 O formaran eso, un loop infinito dándole así más significado a la palabra, también quisiera que las letras fueran cursivas

Y para la palabra "Adidas" no sé como explicarlo en palabras así que voy a dejar los bocetos de las 2 palabras aquí abajo:

<img width="2160" height="1620" alt="IMG_1429" src="https://github.com/user-attachments/assets/e734cdd2-e4d4-4419-8e4c-20fb777d309a" />

## Actividad 2

### Códigos de los experimentos

**Exp #1**

```js
const { Engine, Body, Bodies, Composite } = Matter;

let engine, world;
let bodies = [];

function setup() {
  createCanvas(800, 600);
  engine = Engine.create();


  const estatico = { isStatic: true };
  const piso = Bodies.rectangle(width / 2, height - 20, width, 40, estatico);
  const muroIzq = Bodies.rectangle(10, height / 2, 20, height, estatico);
  const muroDer = Bodies.rectangle(width - 10, height / 2, 20, height, estatico);
  
  
  
  
  bodies.push(piso,muroIzq,muroDer);
  Composite.add(engine.world, [piso, muroIzq, muroDer]);
  
  
  
}

function draw() {
  background(0);
  Engine.update(engine, 1000 / 60);
  noStroke();
  fill(255);
  
  for (let i = 0; i < bodies.length; i++) {
    const v = bodies[i].vertices;
    beginShape();
    for (let j = 0; j < v.length; j++) vertex(v[j].x, v[j].y);
    endShape(CLOSE);
    
    
    
  }
  
  
}

function mousePressed() {
  let b;
  if (random() < 0.5) {
    const ancho = random(20, 60), alto = random(20, 60);
    cuerpo = Bodies.rectangle(mouseX, mouseY, ancho, alto, { restitution: 0.6 });
    
  } else {
    const radio = random(12, 32);
    cuerpo = Bodies.circle(mouseX, mouseY, radio, { restitution: 0.8 });
    
    
  }
  
  bodies.push(cuerpo);
  Composite.add(engine.world,cuerpo);
}

```

**Exp #2**

```js
const { Engine, Bodies, Composite } = Matter;

let engine, bodies = [], t = 0;

function setup() {
  createCanvas(800, 600);
  engine = Engine.create();

  const s = { isStatic: true };

  const piso  = Bodies.rectangle(width/2, height-10, width, 20, s);
  const techo = Bodies.rectangle(width/2, 10, width, 20, s);
  const izq   = Bodies.rectangle(10, height/2, 20, height, s);
  const der   = Bodies.rectangle(width-10, height/2, 20, height, s);


  bodies.push(piso, techo, izq, der);
  Composite.add(engine.world, [piso, techo, izq, der]);


}

function draw() {
  background(0);
  t += 0.01;

  engine.world.gravity.x = cos(t) * 1.2;
  engine.world.gravity.y = sin(t) * 1.2;


  Engine.update(engine);
  noStroke();

  fill(255);
  for (let i = 0; i < bodies.length; i++) {
    const v = bodies[i].vertices;
    beginShape();
    for (let j = 0; j < v.length; j++) vertex(v[j].x, v[j].y);
    endShape(CLOSE);
  }
}

function mousePressed() {
  const r = random(10, 24);
  const b = Bodies.circle(mouseX, mouseY, r, { restitution: 0.6, friction: 0.02 });
  bodies.push(b);
  Composite.add(engine.world, b);
}

```

<img width="804" height="604" alt="image" src="https://github.com/user-attachments/assets/dd6960bf-99b2-4e6e-90b0-f0e3220dc828" />

<img width="799" height="603" alt="image" src="https://github.com/user-attachments/assets/171bd965-790b-413a-b9f4-49ea0e890a7f" />


### Explicación conceptos

**Engine:**

Este es el corazón de todo esto, ya que este es el encargado de que todas las físicas, propiedades, etc., se apliquen correctamente en el entorno o "world".

**World:**

Es el lugar donde nuestra "simulación" va a suceder, esta es la encargada de contener a todos los cuerpos, objetos y constraints. Si un objeto no está dentro de este entorno, no se tendrá en cuenta

**Bodies:**

Es el método para crear cuerpos específicos que ya poseen una plantilla predeterminada como circulos, rectangulos, polígonos etc. 

**Constraint:**

Siento que la mejor manera de explicar que es una constraint, es viéndolo literalmente como una cuerda que en un extremo está pegada a un punto fijo y el otro extremo está atada al objeto, o también incluso se puede usar para unir 2 objetos.


**MouseConstraint:**

Es exactamente lo mismo que una constraint normal solo que ahora el punto fijo al que estaría pegado la cuerda es el mouse :D.

### Dificultades

Al momento de experimentar no quería hacer una clase para cada objeto diferente que saliera (en el exp #1 hay un random que determina la figura que sale cada vez que das click) así que intenté investigar por mi cuenta como podría hacer esto pero fallé estrepitosamente. Le pregunté a chatgpt como lo haría él y me solucionó la vida en 6 líneas de código xd. Me entregó un for que se encarga de analizar los vértices del cuerpo que le llega y con un beginShape y un endShape me hace los circulos y cuadrados. 

Eso fue lo único que se me complicó de resto siento que estuvo todo bien













