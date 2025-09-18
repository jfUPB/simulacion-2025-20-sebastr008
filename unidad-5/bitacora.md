# Evidencias de la unidad 5

## Actividad 2

### Ejemplo 1:

**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

La creación de partículas empieza por la creación del array "particles" en dónde se almacenarán las partículas a mostrar. La clase "particle" tiene un constructor en dónde se le define la posicion de la particula, su aceleración, su velocidad y por último su "lifespan" también hay métodos dentro de esta clase como el run() que se encarga de llamar a los métodos "auxiliares" por así decirlo (métodos para aplicar fuerzas, mostrar la partícula y actualizar la velocidad y la aceleración). La desaparición de las partículas está hecha de manera que en cuanto el lifespan de cierta partícula llega a 0, se le hace un splice a esa particula, sacandola del array y dandole paso a la siguiente partícula para que se genere. Importante analizar que el for encargado lo está recorriendo de atrás para adelante para así evitar saltarse posiciones del array y posiblemente perder información valiosa

**¿Cómo se gestiona la memoria?**

En esta simulación la memoria se gestiona con el array de particulas y el lifespan de cada partícula, cuando el lifespan de una particula es menor a 0, se le hace un splice, eliminando así la referencia de esa partícula y haciendo que la memoria no se vaya llenando cada vez más y más.

En cada frame genero una partícula nueva y actualizo las existentes. Cada partícula tiene un lifespan que decrece; cuando llega a cero, isDead() devuelve true y la elimino del arreglo iterando de atrás hacia adelante para no romper los índices. Al eliminar la referencia, el recolector de basura de JavaScript libera la memoria del objeto. Así gestiono creación, desaparición y memoria sin fugas.

**Modificación al código**

Le implementé un levy flight a la velocidad de las partículas para que así la dirección de las partículas tuviera cierto grado de aleatoriedad. Me basé en el código del capítulo 0 de Nature of Code para implementarlo en la simulación, básicamente añadí una variable "r" que actúa como bandera para saber cuando hacer el salto, la condición es que si esta variable es menor a 0.01, se le añade un "boost" a la velocidad, si no, simplemente se usa la aceleración normal de la gravedad. También añadí un limit para que la velocidad de las partículas que salieran volando con el levy flight no fuera completamente exagerada. Por último quise añadirle un random al lifespan de las partículas para que fuera completamente aleatorio cuando una partícula muere, creando un efecto muy chévere en dónde algunas partículas se ven más transparentes que otras.

**Enlace al código**
[Simulación #1](https://editor.p5js.org/sebastr008/sketches/pL3UAejlS)

**Código fuente**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

let particles = [];

function setup() {
  createCanvas(640, 240);
}

function draw() {
  background(255);
  particles.push(new Particle(width / 2, 20));

  // Looping through backwards to delete
  for (let i = particles.length - 1; i >= 0; i--) {
    let particle = particles[i];
    particle.run();
    if (particle.isDead()) {
      //remove the particle
      particles.splice(i, 1);
    }
  }
}

class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
  }

  run() {
    let gravity = createVector(0, 0.05);
    this.applyForce(gravity);
    this.update();
    this.show();
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  // Method to update position
update() {
  
  const r = random(1);
  let dvx, dvy;
  if (r < 0.01) { 
    dvx = random(-6, 6); 
    dvy = random(-6, 6);
    console.log("Levy!");
  }

  else{ 
    dvx = this.acceleration.x; 
    dvy = this.acceleration.y; 
  } 

  this.velocity.add(this.acceleration);

  this.velocity.x += dvx;
  this.velocity.y += dvy;
  this.velocity.limit(8);

  this.position.add(this.velocity);

  this.lifespan -= 2;
  this.acceleration.mult(0);
}


  // Method to display
  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  // Is the particle still useful?
  isDead() {
    return (this.lifespan < 0.0);
  }
}

```

<img width="646" height="245" alt="image" src="https://github.com/user-attachments/assets/4616b251-4cbf-49bb-867c-4798fd259b26" />
<img width="628" height="259" alt="Captura de pantalla 2025-09-13 120633" src="https://github.com/user-attachments/assets/c0936041-469d-4dfb-bb83-9e5619ad0f35" />



### Ejemplo #2

**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

En este ejemplo, a diferencia del otro que solo tenía un array de partículas, este código permite crear N arrays de partículas por medio de los "emitters" que a su vez estos se guardan en un array llamado emitters.

En el sketch principal se crea un array en dónde se contendrán todos los emisores que se vayan a crear, luego mediante un for que se encarga de recorrer cada emisor dentro de ese array creado, se ejecutan distintos métodos propios de la clase "emitter". Estos métodos son: emitter.run() y emitter.addParticle().

emitter.run(): Es el equivalente al mismo for que había en el sketch.js del ejemplo #1, en dónde mediante un for que se recorre de atrás para adelante para evitar saltarse posiciones del array, se actualizan y se mueren en cuanto el valor de su lifespan es menor a 0 usando el splice que explicamos anteriormente.

emitter.addParticle(): Aquí se crea una partícula y se mete al array "particles" de la clase emitter.

**¿Cómo se gestiona la memoria?**

Se gestiona muy parecido, de hecho, el método es el mismo solo que como ahora hay un array de emitters, pues la función que se encarga de la destrucción de cada partícula está dentro de la clase emitter.

**Modificación al código**

Quise mantener el levy flight del ejemplo pasado, pero ahora aplicado a la velocidad y mezclado con una aceleración que apunta al mouse. La idea es que las partículas sigan el cursor, pero de vez en cuando tenga esos arranques locos típicos del Lévy. Para hacer esto primero calcule la dirección usando dir = mouse - posición, después, lo normalizo y lo escalo *0.2 para finalmente usarlo como aceleración y al Levy lo único que le cambié es que en el else, se usa la aceleración guiada por el mouse.

Quería un movimiento que las partículas me siguieran pero que no sea predecible. El Lévy aporta esos saltos largos ocasionales que hacen que el sistema se sienta más vivo. Con el umbral 0.01 controlo cada cuánto salta, y qué tan fuerte salta.

**Enlace al código**

[Simulación #2](https://editor.p5js.org/sebastr008/sketches/taS91SqYR)

**Código fuente**

```js
let emitters = [];

function setup() {
  createCanvas(640, 240);
  let text = createP("click to add particle systems");
}

function draw() {
  background(255);
  for (let emitter of emitters) {
    emitter.run();
    emitter.addParticle();
  }
}

function mousePressed() {
  emitters.push(new Emitter(mouseX, mouseY));
}

class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
  }

  run() {
    let gravity = createVector(0, 0.05);
    this.applyForce(gravity);
    this.update();
    this.show();
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  // Method to update position
  update() {
    let mouse = createVector(mouseX,mouseY);
    
    let dir = p5.Vector.sub(mouse, this.position);
    dir.normalize();
    dir.mult(0.2);
    
    this.acceleration = dir;
  
  const r = random(1);
  let dvx, dvy;
  if (r < 0.01) { 
    dvx = random(-6, 6); 
    dvy = random(-6, 6);
    
    this.velocity.x += dvx;
    this.velocity.y += dvy;
    console.log("Levy!");
  }

  else{ 
    this.velocity.add(this.acceleration);
  } 

  this.velocity.limit(8);

  this.position.add(this.velocity);

  this.lifespan -= 2;
  this.acceleration.mult(0);
}

  // Method to display
  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  // Is the particle still useful?
  isDead() {
    return this.lifespan < 0.0;
  }
}

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  run() {
    // Looping through backwards to delete
    for (let i = this.particles.length - 1; i >= 0; i--) {
      this.particles[i].run();
      if (this.particles[i].isDead()) {
        // Remove the particle
        this.particles.splice(i, 1);
      }
    }

```

<img width="634" height="272" alt="image" src="https://github.com/user-attachments/assets/61ae7018-026d-4822-8c94-8b49dd1d2222" />

### Ejemplo #3

Como este ejemplo es bastante parecido al segundo no profundizaré tanto en él, explicaré los cambios más significativos.

**¿Cómo se está gestionando la creación y la desaparición de las partículas?**

Ee un solo emisor (ya aqui no hay array de emisores) que crea dos tipos: Particle y Confetti, esta es una clase que hereda todo lo de particles, pero modifica el show() de esta clase, en vez de que salgan circulitos salen cuadrados y se van rotando segun su posición, cada tipo de particula tiene 50/50 de salir. Se guardan en el mismo array y se borran igual (bucle inverso + splice).

**¿Cómo se gestiona la memoria?**

Igual que antes. El polimorfismo no cambia la gestión: al quitar la referencia, el garbage collector lo limpia. Lo único “extra” es que Confetti solo sobrescribe show() para dibujar un cuadrado rotado.

Al mezclar tipos en el mismo array, el coste sigue siendo igual, pero el resultado visual es más variado sin tocar la lógica de borrado. Si subo la tasa de emisión, sube la cantidad de partículas y bajan los FPS, así que se mantiene un límite por emisor.

A este ejemplo le quise aplicar la resistencia del aire un poco simplificada, me basé en la unidad 3 para implementar esta fuerza.

En la función run() de la clase particles fue en dónde añadí la lógica encargada de la resistencia ya que en esta función es en dónde se le está aplicando la gravedad a cada partícula. Lo apliqué de la siguiente manera:

Copié el vector de velocidad a un vector nuevo llamado "fricción" (en realidad es resistencia pero asi le puse y me dio pereza cambiar los nombres de las variables jajaja), luego, los componentes de ese vector los multiplique por menos el coeficiente de resistencia, quedando así un vector proporcional a la velocidad pero apuntando en contra. Finalmente este vector "fricción" se lo pase al método applyForce para que se le aplicara esta fuerza a cada partícula generada.

Lo quise hacer con la resistencia del aire ya que en la unidad 3 hicimos fricción pero tenía ganas de probar a hacer algo con la resistencia del aire así que esto fue la oportunidad perfecta, aparte me ayudó a practicar el concepto de modelar y aplicar fuerzas a una partícula

**Enlace al código**

[Simulación #3](https://editor.p5js.org/sebastr008/sketches/mXNhoLO6a)


**Código fuente**

```js
let emitter;

function setup() {
  createCanvas(640, 240);
  emitter = new Emitter(width / 2, 20);
}

function draw() {
  background(255);
  emitter.addParticle();
  emitter.run();
}

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    let r = random(1);
    if (r < 0.5) {
      this.particles.push(new Particle(this.origin.x, this.origin.y));
    } else {
      this.particles.push(new Confetti(this.origin.x, this.origin.y));
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      let p = this.particles[i];
      p.run();
      if (p.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}

class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
  }

  run() {
    let gravity = createVector(0, 0.05);
    let friction = this.velocity.copy();
    let c = 0.04;
    friction.mult(-c);
    
    
    this.applyForce(gravity);
    this.applyForce(friction);
    this.update();
    this.show();
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  // Method to update position
  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.lifespan -= 2;
    this.acceleration.mult(0);
  }

  // Method to display
  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  isDead() {
    return this.lifespan < 0.0;
  }
}

class Confetti extends Particle {
  // Override the show method
  show() {
    let angle = map(this.position.x, 0, width, 0, TWO_PI * 2);

    rectMode(CENTER);
    fill(127, this.lifespan);
    stroke(0, this.lifespan);
    strokeWeight(2);
    push();
    translate(this.position.x, this.position.y);
    rotate(angle);
    square(0, 0, 12);
    pop();
  }
}
```

<img width="272" height="248" alt="image" src="https://github.com/user-attachments/assets/7268f0ec-3220-40d6-a037-98e9001fd488" />


### Ejemplo #4

Este ejemplo es también similar, solo con un pequeño cambio en como se aplican las fuerzas a las partículas.

**¿Cómo se está gestionando la creación y la desaparición de las partículas?**

En esta versión se tiene también un solo Emitter. En cada draw() se hace primero emitter.addParticle() (crea 1 partícula en origin) y luego emitter.run() (actualiza, dibuja y limpia).
Dentro de run() recorro de atrás hacia adelante el array particles; cuando isDead() (porque el lifespan llegó a 0), hago splice(i,1) para sacarla sin romper índices.

Aquí va el detalle, las fuerzas (gravedad, viento, etc.) se preparan afuera y se inyectan al emisor para que él se las aplique a todas sus partículas. Además, ahora applyForce usa la segunda ley de Newton: como lo que modifico es la aceleración, divido la fuerza entre la masa (a += F / m) y luego integro normal en update(). Eso deja cambiar/combinar fuerzas sin tocar la clase Particle y además puedo decidir por emisor qué fuerza usar.

**¿Cómo se gestiona la memoria?**

Cuando hago splice a una partícula muerta, pierde la referencia y el GC de JavaScript la libera después. Para no generar basura innecesaria, reutilizo vectores constantes (por ejemplo una GRAVITY global) en vez de crear un createVector idéntico por partícula y por frame.

A este ejemplo el concepto que quise aplicar es el del movimiento oscilatorio, ya que fue el efecto con el que su resultado me pareció más bonito en la aplicación que hice para esa unidad, lo apliqué siguiendo la misma lógica que propone el ejemplo de aplicar la fuerza desde afuera y ponersela al emitter para que se las aplique a cada una de las partículas. En el sketch.js lo que hice fue añadir 2 variables (theta y omega, theta siendo el ángulo y omega la velocidad angular). También cree una variable windAmp que entre más grande sea esta variable, más duro empujará el viento. 

Ahora dentro del draw cree un vector nuevo que será el viento que empuje estas partículas de un lado a otro, el componente en x de este vector será windAmp * sin(theta), después para cambiar el ángulo, le sumaremos omega al ángulo theta para que haya cambio en la dirección de cada partícula. Finalmente este vector viento será el que le pasaremos al método applyForce del emitter, que será ya el encargado de aplicarle esta fuerza sinusoidal a cada partícula.

[Enlace al códigp](https://editor.p5js.org/sebastr008/sketches/PB7KJ4JIV)

```js

let emitter;
const windAmp = 0.05;

let theta = 0;
const OMEGA = 0.08;

function setup() {
  createCanvas(1280, 480);
  emitter = new Emitter(width / 2, 50);
}

function draw() {
  background(255, 30);

  const gravity = createVector(0, 0.1);
  const wind    = createVector(windAmp * sin(theta), 0);
  theta += OMEGA;

  emitter.applyForce(gravity);
  emitter.applyForce(wind);

  emitter.addParticle();
  emitter.run();
}

class Emitter {

  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    //{!3} Using a for of loop to apply the force to all particles
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  run() {
    //{!7} Can’t use the enhanced loop because checking for particles to delete.
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}

class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0.0);
    this.velocity = createVector(random(-1, 1), random(-2, 0));
    this.lifespan = 255.0;
    this.mass = 1; // Let's do something better here!

  }

  run() {
    this.update();
    this.show();
  }

  applyForce(force) {
    let f = force.copy();
    f.div(this.mass);
    this.acceleration.add(f);
  }

  // Method to update position
  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    this.lifespan -= 2.0;
  }

  // Method to display
  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  // Is the particle still useful?
  isDead() {
    return this.lifespan < 0.0;
  }
}

```

<img width="407" height="458" alt="image" src="https://github.com/user-attachments/assets/97d66d94-d5e5-4bc9-b6f4-ad65f8edcd2e" />

**Se puede apreciar un poco en la imagen como se ve la forma sinusoidal en el recorrido de las partículas**

### Ejemplo 5 

En el ejemplo #5, el patrón es el mismo de los otros ejemplos, solo cambian dos cosas (repulsor y el método para aplicarlo).

**¿Cómo se está gestionando la creación y la desaparición de las partículas?**

En cada draw() se llama emitter.addParticle() y el emisor hace push(new Particle(origin.x, origin.y)) sobre su arreglo interno. 

Se aplica gravedad a todas las partículas con emitter.applyForce(gravity) y, además, una fuerza de repulsión calculada por partícula con emitter.applyRepeller(repeller); el repulsor implementa repel(particle) (inversa de la atracción, con distancia acotada y power). 
natureofcode.com
 
Cada partícula tiene lifespan que decrece y un isDead(); el emisor recorre el arreglo de atrás hacia adelante y hace splice(i,1) cuando isDead() es true. Esto evita saltarse elementos al borrar.

**¿Cómo se gestiona la memoria?**

Al hacer splice se elimina la referencia en el arreglo y el garbage collector del navegador recupera la memoria cuando ya no hay referencias. No hay límites duros; el equilibrio lo da el ritmo de spawn y la muerte por lifespan

La modificación que le hice a este ejemplo utilizó 2 conceptos, uno de la unidad 4 y otro de la unidad 3, en dónde le aplico fuerzas al repeller para moverlo con WASD y le aplico una micro-oscilación angular a las partículas que salen.

[Enlace al código](https://editor.p5js.org/sebastr008/sketches/jcP0cXrKe)

```js
// Example 4.7 — Particle System with a Repeller (p5.js)
// + Mod 1: micro-oscilación angular en cada partícula
// + Mod 2: Repeller con física controlado por WASD (fuerzas de la Unidad 3)

let emitter;
let repeller;

function setup() {
  createCanvas(640, 240);
  emitter = new Emitter(width / 2, 20);
  repeller = new Repeller(width / 2, 200);
}

function draw() {
  background(255);

  // Spawnea 1 partícula por frame
  emitter.addParticle();

  // Fuerzas globales
  const gravity = createVector(0, 0.1);
  emitter.applyForce(gravity);

  // --- Control del repeller por WASD aplicando fuerzas ---
  handleInput();     // aplica fuerzas al repeller según teclas
  repeller.update(); // integra posición/velocidad + damping/bordes

  // Repulsor afectando partículas
  emitter.applyRepeller(repeller);

  // Actualiza/dibuja y limpia muertas
  emitter.run();

  // Dibuja el repulsor
  repeller.show();
}

// Pequeña función para leer WASD y aplicar una fuerza al repeller
function handleInput() {
  const f = 0.4;                 // magnitud de la fuerza por tecla
  const force = createVector(0, 0);
  if (keyIsDown(87)) force.y -= f; // W: arriba
  if (keyIsDown(83)) force.y += f; // S: abajo
  if (keyIsDown(65)) force.x -= f; // A: izquierda
  if (keyIsDown(68)) force.x += f; // D: derecha
  if (force.magSq() > 0) repeller.applyForce(force);
}

// ------------------ Classes ------------------

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  applyRepeller(repeller) {
    for (let particle of this.particles) {
      const force = repeller.repel(particle);
      particle.applyForce(force);
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.run();
      if (p.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}

class Repeller {
  constructor(x, y) {
    this.position = createVector(x, y);
    // --- Física del repeller ---
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    this.mass = 1;

    this.power = 150; // intensidad de repulsión sobre partículas
    this.radius = 16; // radio visual (circle de 32 px)
  }

  // Fuerzas del mundo (WASD) aplicadas al repeller
  applyForce(f) {
    // a = F / m
    const a = p5.Vector.div(f, this.mass);
    this.acceleration.add(a);
  }

  update() {
    // Integración típica (Motion 101)
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);

    // Pequeño amortiguamiento para que no se dispare
    this.velocity.mult(0.98);

    // Limitar dentro del lienzo (clamp suave)
    if (this.position.x < this.radius) {
      this.position.x = this.radius; this.velocity.x = 0;
    }
    if (this.position.x > width - this.radius) {
      this.position.x = width - this.radius; this.velocity.x = 0;
    }
    if (this.position.y < this.radius) {
      this.position.y = this.radius; this.velocity.y = 0;
    }
    if (this.position.y > height - this.radius) {
      this.position.y = height - this.radius; this.velocity.y = 0;
    }
  }

  show() {
    stroke(0);
    fill(127);
    circle(this.position.x, this.position.y, this.radius * 2);
  }

  repel(particle) {
    // Igual que un atractor, pero fuerza negativa (aleja)
    let force = p5.Vector.sub(this.position, particle.position);
    let d = force.mag();
    d = constrain(d, 5, 50);
    const strength = -1 * this.power / (d * d);
    force.setMag(strength);
    return force;
  }
}

class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.velocity = createVector(random(-1, 1), random(-2, 0));
    this.acceleration = createVector(0, 0);
    this.lifespan = 255;

    // ---- Mod: oscilador angular sutil ----
    this.angle = random(TWO_PI);
    this.aVelocity = 0;
    this.aAcceleration = 0;
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  run() {
    this.update();
    this.show();
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    this.lifespan -= 2;

    // ---- Mod: "latido" depende levemente de la rapidez ----
    this.aAcceleration = constrain(this.velocity.mag() * 0.002, -0.05, 0.05);
    this.aVelocity += this.aAcceleration;
    this.angle += this.aVelocity;
  }

  show() {
    stroke(0, this.lifespan);
    fill(175, this.lifespan);
    const s = map(sin(this.angle), -1, 1, 4, 9); // tamaño oscilante
    circle(this.position.x, this.position.y, s);
  }

  isDead() {
    return this.lifespan < 0;
  }
}

```

<img width="631" height="241" alt="image" src="https://github.com/user-attachments/assets/4b884648-ee1d-45b8-b5fd-f58366590419" />

## Actividad 3

### Proceso de conceptualización

Primeramente quise buscar inspiración en Pinterest para poder encaminar el proyecto hacia una estética o sentimiento, hasta el momento empecé con una moodboard en el cual agrego imágenes que siento que pueden estar relacionadas y seguidamente pongo en palabras lo que me transmitene esas imágenes a medida que las veo. Hasta el momento llevo esto:

<img width="921" height="829" alt="image" src="https://github.com/user-attachments/assets/1eece35f-43db-4570-917e-0885f06c1bff" />

 
También le pregunté a mi novia que palabras se le ocurrían al ver estas imágenes y las palabras que me dijo fueron:

-	Aura
-	Renacimiento
-	Creación
-	Exploración
-	Esencia
-	Universos
-	Energía
-	Despertar
-	Eternidad
-	Cosmos
-	Espíritu
-	Viaje
-	Portal
-	Plenitud
-	Paz
-	Origen

Puse todas estas palabras en el moodboard y me quedé analizándolas un tiempo.

Luego quise hacer combinaciones de todas las palabras que pensé junto con las que me dio mi novia para empezar a encaminar el concepto del proyecto y pude llegar a varias combinaciones interesantes por mi cuenta. Estas fueron las combinaciones a las que pude llegar:


 <img width="681" height="178" alt="image" src="https://github.com/user-attachments/assets/9ed339a9-9058-4a21-b2c9-809216267b85" />


Al hacer esta combinación de palabras, me quedó sonando la siguiente con la cuál me empezaron a surgir las primeras ideas:

**Más allá / Exploración / Dimensión / Universos / Creación / Portal.**

Le estuve dando muchas vueltas a esta combinación de palabras hasta que pude terminar de conectar completamente todas las ideas lo cuál me lleva al concepto de mi proyecto el cuál es:

**Cada parpadeo crea una dimensión maleable en dónde nuestra presencia modela universos y variaciones sutiles producen grandes cambios.**

Luego de tener claro el concepto de lo que quería realizar, me puse a pensar las primeras ideas e hice unos pequeños bocetos que representan a grandes rasgos lo que quería lograr con el proyecto:


<img width="920" height="691" alt="image" src="https://github.com/user-attachments/assets/715072b6-9a8b-40d8-89ff-e8696daed42b" />


Como la detección de la mano aún no sé muy bien como hacer que sea bastante precisa, la interacción creo que solo será con el mouse y lo de tapar la cámara ya que, si quiero hacer todo lo que tengo en mente, no me dará tiempo a implementar una interacción más compleja.

Así que empecé a hacer realidad poco a poco lo que tenía en mi mente, le di un buen contexto a ChatGPT de lo que quería lograr (importante aclarar que no se usó ChatGPT para ninguna decisión de diseño) así que empezó con una primera versión “prototipo” del proyecto.

En esta primera versión, me dio un sistema de partículas muy sencillo en dónde no había sensación de estar viajando por el espacio, pero lo de mostrar los caminos que recorriste dentro de ese universo si me gustó bastante como quedó:


<img width="921" height="863" alt="image" src="https://github.com/user-attachments/assets/c3198314-8b84-4647-9b77-26f34408acc3" />

 
Parece una especie de constelación, sin embargo, estas figuras no eran muy fieles al recorrido del mouse ya que en esa iteración simplemente movi el mouse un poco hacia la derecha, así que no fue muy preciso que digamos.

Primeramente quise corregir el sistema de partículas ya que este no daba la sensación que quería que diera, lo que quería finalmente era sentir que estaba viajando a través de todas esas partículas y que estaba influyendo en ese universo, así que me puse manos a la obra y después de varias correcciones pude llegar a este prototipo:

[Prototipo Sis Particulas](https://editor.p5js.org/sebastr008/sketches/lS1DqO7mu)

<img width="921" height="832" alt="image" src="https://github.com/user-attachments/assets/e0d91f80-25f3-465b-a565-4cb868a99ce0" />


 
Como podemos ver, ahora si se siente que estamos atravesando un universo lleno de partículas, sin embargo aún no teníamos como ese feedback de que estuviéramos haciendo algo dentro de este universo, por ende, quise añadir la interacción del mouse, en donde las partículas se repelen con diferente fuerza del mouse.

No me demore mucho implementando esto, así que de una vez quise empezar a implementar lo de la cámara.

Aquí llegamos a otro prototipo en dónde la interacción de tapar la cámara con un dedo, funcionó a la primera sorprendentemente, lo único que se conservó fue la manera en como salía “el recorrido” del mouse (seguía siendo bastante impreciso)


<img width="921" height="836" alt="image" src="https://github.com/user-attachments/assets/4ca85623-cb4f-451d-96a5-b2fddb12861b" />


** Se aprecian espacios negros, ahí estaba mi mouse solo que la captura de pantalla no muestra el mouse **


Ahora, lo único que quería es cambiar la manera de como se iban a representar esos cambios que hicimos dentro de ese universo, me quedé un rato pensando y dije, “¿Que tal si por donde te muevas, ese es el input para crear un planeta único en cada iteración?” Sinceramente casi me quemo el cerebro pensando en eso así que me puse manos a la obra para implementar

La primera versión fue un completo fracaso jaja, lo único que logré hacer fue que el planeta tuviera color y de vez en cuando salía uno que otro “trazo” sin tener éxito alguno.

<img width="921" height="870" alt="image" src="https://github.com/user-attachments/assets/9193dc4f-19b6-43bd-927e-2ad1c6ec6275" />

 
El 90% de las veces salía esto sin ningún “recorrido” hecho, y aquí ya me estaba empezando a frustrar, así que opte por aislar esta parte del código, programarla bien y una vez que la función esté bien hecha, ahí vincularlo al proyecto.

 
<img width="921" height="696" alt="image" src="https://github.com/user-attachments/assets/be226e26-23b8-4bd0-b283-638e15461d54" />


<img width="921" height="888" alt="image" src="https://github.com/user-attachments/assets/37c2da08-b588-4c8f-9f4a-f5a9fb133546" />



Este fue el resultado que obtuve y FINALMENTE lo pude implementar al proyecto.

Ahora las 3 partes están completas, y creo que el proyecto quedó finalizado.

Tapar y que al destapar estemos en una galaxia – Check
Sensación de estar dentro de una galaxia y modificando sus partículas – Check
Resultado de haber modificado las partículas en las galaxias + potencial souvenir – Check

Fue un proceso bastante divertido de hacer, me gustó mucho idear este concepto y me alegró bastante poder haber llegado al resultado que quise.

[Código proyecto](https://editor.p5js.org/sebastr008/sketches/LCro8M1S2)

**Código fuente**

```js
// ———————————————————————————————————————————————————————————————
// PORTAL — Cosmos 3D (fuerzas + glow) → Geografía final (tierra donde más estuviste)
// Estados: REAL → ENTRADA → COSMOS → PLANETA (IN → SHOW → OUT)
// Click en COSMOS = generar forma • Click en PLANETA (SHOW) = volver a cámara
// Atajos: [M] alternar mano/mouse • [R] reiniciar • [S] guardar PNG
// Incluye: Emitter3D explícito + música (cosmos/planeta) + fade-in de entrada
// ———————————————————————————————————————————————————————————————

let video, prevFrame, w, h;
let state = "REAL";
let darkEMA = 1, wasCovered = false, darkCount = 0;
const DARK_THR = 0.22, COVER_FRAMES = 12;

// — audio —
let bgm = null;              // música del cosmos
let bgmStarted = false;
let bgmPlanet = null;        // música de la geografía final
let planetAudioStarted = false;

let layerTrails, layerFront, layerGlowLow;
const BLOOM_SCALE = 0.5;

let agents = [];
let poolDust = [], poolStreamer = [], poolSpark = [];
let palette;

let cosmosT0 = 0, cosmosDur = 16000, seedRun = 0;

let motion = { x:null, y:null, strength:0 };
let useHand = true;
let fade = 0; // ← se usa para el fade-in al entrar al cosmos

// ——— Viaje 3D ———
const FOCAL = 700;
let speed = 0, speedTarget = 14;
let vpSmooth = {x:0.5, y:0.5};

const KR = 0.015;      // atracción radial
const KT = 0.030;      // swirl tangencial
const FRICTION = 0.985;

let fpsEMA = 60, adjustEvery = 30, frameSinceAdj = 0;
let targetN = 1200;

// ——— Emisor explícito ———
let emitter = null; // instancia de Emitter3D

// ——— Recorrido ———
let pathPoints = [];
let hueVar = 0, lastHue = null;

// Heatmap (donde estuviste)
const HM_W = 96, HM_H = 96;
let heat = [];
let heatSmoothed = null;   // suavizado fino
let heatMaxSmooth = 1;

// — islas grandes (blur macro) —
let heatLand = null;       // grid macro para decidir tierra
let heatLandMax = 1;

// Parámetros planeta/geografía
const LAND_RATIO_TARGET = 0.40; // % de área tierra objetivo (0.30–0.50)
let   LAND_THICKNESS     = 1.5; // perilla de “grosor” del verde
let   FEATHER            = 0.08; // suavizado de costa
const G_SIGMA2 = 1.2*1.2;       // suavizado fino
const G_RAD    = 2;             // 5x5
const LAND_BLUR_RAD   = 5;      // blur macro (≈ 11x11)
const LAND_BLUR_SIGMA = 2.6;    // sigma macro

// — Mensaje inspirador al mostrar el planeta (edítalo fácil aquí) —
let PLANET_MESSAGE = "Pequeñas decisiones escalan a grandes cambios...";

function preload(){
  // Requiere p5.sound
  soundFormats('mp3','ogg');
  bgm = loadSound('cosmoss.mp3');      // cambia la ruta si hace falta
  bgmPlanet = loadSound('planet.mp3'); // cambia la ruta si hace falta
}

function heatInit(){ heat = new Array(HM_W*HM_H).fill(0); }
function heatAdd(x, y, weight){
  const gx = floor(constrain(map(x,0,width, 0,HM_W), 0, HM_W-1));
  const gy = floor(constrain(map(y,0,height,0,HM_H), 0, HM_H-1));
  heat[gy*HM_W + gx] += weight;
}

// ——— Geografía + transiciones ———
let planet = null; // {gfx, R, cx, cy, seed}
let planetPhase = "IN"; // "IN" → "SHOW" → "OUT"
let planetT0 = 0;
const PLANET_IN_DUR = 1200;
const PLANET_OUT_DUR = 900;
const ZOOM_IN_START = 1.20, ZOOM_IN_END = 1.00;
const ZOOM_OUT_END  = 0.85;

function setup(){
  pixelDensity(1);
  w = windowWidth; h = windowHeight;
  createCanvas(w, h); colorMode(HSB,360,100,100,100);

  layerTrails = createGraphics(w, h);    layerTrails.colorMode(HSB,360,100,100,100);
  layerFront  = createGraphics(w, h);    layerFront.colorMode(HSB,360,100,100,100);
  layerGlowLow= createGraphics(w*BLOOM_SCALE, h*BLOOM_SCALE); layerGlowLow.colorMode(HSB,360,100,100,100);

  video = createCapture(VIDEO, ()=>video.size(160,120));
  video.hide(); prevFrame = createImage(160,120);

  heatInit();
  resetToReal();
}

function windowResized(){
  w = windowWidth; h = windowHeight;
  resizeCanvas(w, h);
  layerTrails = createGraphics(w, h);    layerTrails.colorMode(HSB,360,100,100,100);
  layerFront  = createGraphics(w, h);    layerFront.colorMode(HSB,360,100,100,100);
  layerGlowLow= createGraphics(w*BLOOM_SCALE, h*BLOOM_SCALE); layerGlowLow.colorMode(HSB,360,100,100,100);
}

function draw(){
  background(0);
  analyzeCamera();

  if (state==="REAL"){ drawReal(); detectCoverRelease(); return; }

  if (state==="ENTRADA"){
    // Fade-in suave del cosmos
    speedTarget = lerp(speedTarget, 18, 0.08);
    drawCosmos3D();
    fade = min(1, fade + 0.05);

    // Capa negra que se desvanece (alpha 100→0)
    push(); noStroke(); fill(0,0,0, (1 - fade) * 100); rect(0,0,width,height); pop();

    if (fade>=1){ state="COSMOS"; cosmosT0 = millis(); }
    return;
  }

  if (state==="COSMOS"){
    // oscilación global (pulso)
    const t = (millis()-cosmosT0)/cosmosDur;
    speedTarget = 13.5 + 1.2*sin(t*TAU*0.5);
    drawCosmos3D();

    // ——— registrar recorrido (siempre: mano si hay; si no, mouse)
    const px = (useHand && motion.x != null) ? motion.x : mouseX;
    const py = (useHand && motion.y != null) ? motion.y : mouseY;

    if (frameCount % 2 === 0){
      const moved = (typeof pmouseX !== "undefined") ? dist(px, py, pmouseX, pmouseY) : 999;
      const dwell = moved < 1.5 ? 2.0 : 1.0;
      const str = (useHand && motion.x != null) ? motion.strength : (mouseIsPressed ? 1 : 0.35);

      pathPoints.push({ x: px, y: py });
      heatAdd(px, py, (0.6 + 1.2 * str) * dwell);
    }
    return;
  }

  if (state==="PLANETA"){
    drawPlanet();
    return;
  }
}

// —————————————————— REAL + transición ——————————————————
function drawReal(){
  push(); translate(width,0); scale(-1,1); image(video,0,0,width,height); pop();
  hud("Cubre el lente 1–2 s y suelta para entrar");
}
function detectCoverRelease(){
  if (darkEMA < DARK_THR){ darkCount++; if (darkCount>COVER_FRAMES) wasCovered=true; }
  else { if (wasCovered){ enterCosmos(); } darkCount=0; wasCovered=false; }
}
function enterCosmos(){ 
  initCosmos3D(); 
  state="ENTRADA"; 
  fade=0; 
  startBGM();        // música del cosmos (fade-in)
}

function resetToReal(){
  state="REAL"; fade=0; wasCovered=false; darkCount=0;
  layerTrails.clear(); layerFront.clear(); layerGlowLow.clear();
  agents.length=0; poolDust.length=0; poolStreamer.length=0; poolSpark.length=0;
  pathPoints.length=0; hueVar=0; lastHue=null;
  heatInit(); heatSmoothed=null; heatMaxSmooth=1; heatLand=null; heatLandMax=1;
  speed=0; speedTarget=14; vpSmooth={x:0.5,y:0.5};
  planet=null; planetPhase="IN"; planetT0=0;
  emitter = null;
  stopPlanetBGM();   // asegura que no quede sonando
  stopBGM();
}

// —————————————————— Cosmos 3D con fuerzas + glow + EMITTER ——————————————————
function initCosmos3D(){
  seedRun = floor(random(1e9)); randomSeed(seedRun); noiseSeed(seedRun);
  palette = choosePalette();

  layerTrails.clear(); layerFront.clear(); layerGlowLow.clear();

  targetN = constrain(floor((width*height)/800), 700, 2200);

  // Prefill parcial
  const prefill = floor(targetN * 0.7);
  for (let i=0;i<prefill;i++) spawnAgent3D();

  // Emisor explícito
  emitter = new Emitter3D({ rate: 800, max: targetN });

  // punto de fuga de arranque (ligeramente descentrado)
  vpSmooth = { x: 0.45 + random(-0.05,0.05), y: 0.50 + random(-0.05,0.05) };
}

function drawCosmos3D(){
  // dirigir VP con mano/mouse
  const tx = constrain((useHand && motion.x!==null ? motion.x/width : mouseX/width), 0,1);
  const ty = constrain((useHand && motion.y!==null ? motion.y/height: mouseY/height),0,1);
  vpSmooth.x = lerp(vpSmooth.x, tx, 0.06);
  vpSmooth.y = lerp(vpSmooth.y, ty, 0.06);

  // trails
  layerTrails.noStroke(); layerTrails.fill(0,0,0,12); layerTrails.rect(0,0,width,height);
  layerFront.clear();

  // update/render
  for (let i=agents.length-1; i>=0; i--){
    const a = agents[i];
    a.update3D();
    a.render3D(layerTrails, layerFront);
    if (a.dead){ recycle3D(a); agents.splice(i,1); }
  }

  // Emisión por tasa (emisor explícito)
  if (emitter){ emitter.max = targetN; emitter.update(deltaTime); }

  compositeGlow();

  // FPS adaptativo
  fpsEMA = 0.9*fpsEMA + 0.1*(1000/deltaTime);
  if (++frameSinceAdj >= adjustEvery){
    frameSinceAdj=0;
    if (fpsEMA<53 && targetN>600) targetN=floor(targetN*0.90);
    else if (fpsEMA>58 && targetN<2200) targetN=floor(targetN*1.05);
  }

  hud(`Explora con ${useHand? "mano (cámara)" : "mouse"} • Click: ver geografía • [M] alternar • [R] reiniciar • [S] guardar`);
}

function compositeGlow(){
  image(layerTrails,0,0);
  layerGlowLow.clear();
  layerGlowLow.image(layerFront, 0,0, layerGlowLow.width, layerGlowLow.height);
  layerGlowLow.filter(BLUR, 3);
  push(); blendMode(ADD);
  image(layerGlowLow, 0,0, width, height);
  image(layerFront, 0,0);
  layerGlowLow.filter(BLUR, 1.6);
  image(layerGlowLow, 0,0, width, height);
  pop();
}

// —————————————————— Emitter3D ——————————————————
class Emitter3D {
  constructor({ rate = 600, max = 1200 } = {}) {
    this.rate = rate;   // partículas/segundo
    this.max  = max;    // techo poblacional
    this.acc  = 0;      // acumulador fraccional
  }
  update(dt = 16.67) {
    this.max = targetN;
    this.acc += this.rate * (dt/1000);
    while (this.acc >= 1 && agents.length < this.max) {
      this.emitOne();
      this.acc -= 1;
    }
  }
  emitOne(){
    const r = random();
    let o;
    if (r < 0.55) o = poolDust.pop()     || new Dust3D();
    else if (r < 0.85) o = poolStreamer.pop() || new Streamer3D();
    else               o = poolSpark.pop()    || new Spark3D();
    o.reset3D(); // posiciona respecto a vpSmooth y setea z/vida/tono
    agents.push(o);
  }
}

// —————————————————— Partículas 3D (Herencia/Polimorfismo) ——————————————————
class Particle3D{
  constructor(){ this.alive=false; }
  reset3D(kind){
    const cx = vpSmooth.x*width, cy = vpSmooth.y*height;
    this.x=(random()-0.5)*1.2*width+cx;
    this.y=(random()-0.5)*1.2*height+cy;
    this.z=random(200,2000);
    this.vx=0; this.vy=0; this.vz=0;
    this.px=null; this.py=null;
    this.h=pickHue(); this.s=random(55,90); this.b=100;
    this.life=600 + random(600);
    this.kind=kind||"dust";
    this.alive=true; this.dead=false;
    this.theta=random(TAU); this.omega=random(-0.02,0.02);
  }
  applyForces(){
    // avance en profundidad
    speed += (speedTarget - speed)*0.05;
    this.z -= speed * (0.6 + 0.4*(1 - this.z/2000));

    const cx = vpSmooth.x*width, cy = vpSmooth.y*height;
    let dx=this.x-cx, dy=this.y-cy;
    const r2=dx*dx+dy*dy+1e-3, invR=1/Math.sqrt(r2);
    const depthF = 0.6 + 0.4*(1 - this.z/2000);

    // fuerzas base: atracción radial + swirl tangencial
    let ax = -KR*dx*invR*depthF + KT*(-dy)*invR*depthF;
    let ay = -KR*dy*invR*depthF + KT*( dx)*invR*depthF;

    // —— Unidad 4: oscilación/sinusoidal sutil (angular) ——
    this.theta += this.omega;                     // fase propia
    const wiggle = 0.02 * sin(this.theta) * depthF;  // amplitud pequeña y estable
    ax += wiggle * (-dy) * invR;  // ondulación tangencial
    ay += wiggle * ( dx) * invR;

    // interacción con mano/mouse (en coords de pantalla proyectadas)
    const scale = FOCAL/this.z;
    const sx = cx + (this.x-cx)*scale;
    const sy = cy + (this.y-cy)*scale;

    let px = useHand&&motion.x!==null? motion.x: mouseX;
    let py = useHand&&motion.y!==null? motion.y: mouseY;

    // radio variable (empieza grande y se reduce con el tiempo de viaje)
    const t = state==="COSMOS" ? constrain((millis()-cosmosT0)/cosmosDur,0,1) : 0;
    const strength = useHand&&motion.x!==null? motion.strength: (mouseIsPressed?1:0.35);
    const baseR = lerp(140, 80, t);
    const r = (baseR + 220*strength*lerp(1,0.65,t)) * 0.9;

    const dxs = sx-px, dys = sy-py; const ds = Math.sqrt(dxs*dxs+dys*dys);
    if (ds<r){
      const k = (this.z/FOCAL);
      const rx = dxs*k, ry = dys*k;
      const mag = map(ds, 0, r, 1.4, 0.07);
      ax += (rx/ds)*mag; ay += (ry/ds)*mag;
      // deriva cromática con interacción
      this.h = (this.h + 0.8 + 1.6*strength) % 360;
    } else {
      this.h = (this.h + 0.02) % 360;
    }

    // integrar
    this.vx = (this.vx + ax) * FRICTION;
    this.vy = (this.vy + ay) * FRICTION;
    this.x += this.vx; this.y += this.vy;

    // ciclo de vida / reciclaje por profundidad
    this.life--; if (this.z<=2.5){ this.z=2000; this.px=null; this.py=null; }
    if (this.life<=0){ this.dead=true; }
  }
  update3D(){ this.applyForces(); }
  render3D(pgT, pgF){
    const cx = vpSmooth.x*width, cy = vpSmooth.y*height;
    const scale = FOCAL/this.z;
    const sx = cx + (this.x-cx)*scale;
    const sy = cy + (this.y-cy)*scale;

    if (this.px!==null){
      const len = dist(this.px,this.py,sx,sy);
      const w = map(this.z, 2000, 2.5, 0.5, 4.0);
      const aB = map(constrain(len,0,80), 0,80, 14, 60);
      const aF = aB*1.15;

      pgT.stroke(this.h, 60, 95, aB*0.55);
      pgT.strokeWeight(w*this.trailScale());
      pgT.line(this.px,this.py, sx,sy);

      pgF.stroke(this.h, this.s, 100, aF);
      pgF.strokeWeight(w*1.2*this.frontScale());
      pgF.line(this.px,this.py, sx,sy);
      pgF.noStroke(); pgF.fill(this.h, this.s, 100, aF*0.5);
      pgF.circle(sx,sy, w*6*this.frontScale());
      updateHueVariance(this.h);
    }
    this.px = sx; this.py = sy;
  }
  trailScale(){ return 1.0; }
  frontScale(){ return 1.0; }
}
class Dust3D extends Particle3D{}
class Streamer3D extends Particle3D{
  update3D(){ this.applyForces(); this.vx+=0.03*this.vx; this.vy+=0.03*this.vy; }
  trailScale(){ return 1.1; } frontScale(){ return 1.2; }
}
class Spark3D extends Particle3D{
  update3D(){ this.applyForces(); this.omega += random(-0.008,0.008); }
  trailScale(){ return 0.9; } frontScale(){ return 1.35; }
}
function spawnAgent3D(){
  const r=random();
  if (r<0.55){ const o=poolDust.pop()||new Dust3D(); o.reset3D("dust"); agents.push(o); }
  else if (r<0.85){ const o=poolStreamer.pop()||new Streamer3D(); o.reset3D("streamer"); agents.push(o); }
  else { const o=poolSpark.pop()||new Spark3D(); o.reset3D("spark"); agents.push(o); }
}
function recycle3D(o){
  o.alive=false; o.dead=false;
  if (o.kind==="dust") poolDust.push(o);
  else if (o.kind==="streamer") poolStreamer.push(o);
  else poolSpark.push(o);
}

// —————————————————— Heat suavizado y utilidades geografía ——————————————————
function rebuildHeatSmoothed(){
  // gauss fino 5x5
  const RAD=G_RAD, sigma2=G_SIGMA2;
  if (!heatSmoothed) heatSmoothed = new Array(HM_W*HM_H).fill(0);
  let maxV = 0;
  for (let y=0; y<HM_H; y++){
    for (let x=0; x<HM_W; x++){
      let sum=0, wsum=0;
      for (let j=-RAD; j<=RAD; j++){
        const yy = constrain(y+j, 0, HM_H-1);
        for (let i=-RAD; i<=RAD; i++){
          const xx = constrain(x+i, 0, HM_W-1);
          const w = Math.exp(-(i*i + j*j)/(2*sigma2));
          sum  += heat[yy*HM_W + xx] * w;
          wsum += w;
        }
      }
      const v = sum / Math.max(wsum, 1e-6);
      heatSmoothed[y*HM_W + x] = v;
      if (v > maxV) maxV = v;
    }
  }
  heatMaxSmooth = maxV || 1;
}

// blur macro (islas grandes)
function rebuildHeatLand(){
  const RAD = LAND_BLUR_RAD;
  const sigma2 = LAND_BLUR_SIGMA * LAND_BLUR_SIGMA;

  if (!heatLand) heatLand = new Array(HM_W*HM_H).fill(0);

  let maxV = 0;
  for (let y=0; y<HM_H; y++){
    for (let x=0; x<HM_W; x++){
      let sum=0, wsum=0;
      for (let j=-RAD; j<=RAD; j++){
        const yy = constrain(y+j, 0, HM_H-1);
        for (let i=-RAD; i<=RAD; i++){
          const xx = constrain(x+i, 0, HM_W-1);
          const w = Math.exp(-(i*i + j*j)/(2*sigma2));
          sum  += heatSmoothed[yy*HM_W + xx] * w;
          wsum += w;
        }
      }
      const v = sum / Math.max(wsum, 1e-6);
      heatLand[y*HM_W + x] = v;
      if (v > maxV) maxV = v;
    }
  }
  heatLandMax = maxV || 1;
}

// Bilinear desde un grid (0..1)
function sampleHeat01From(grid, gridMax, px, py, R){
  // px,py en coords del disco [-R..R]
  const u = constrain((px+R)/(2*R), 0, 1);
  const v = constrain((py+R)/(2*R), 0, 1);
  const gx = u*(HM_W-1), gy = v*(HM_H-1);
  const x0=floor(gx), y0=floor(gy);
  const x1=min(HM_W-1, x0+1), y1=min(HM_H-1, y0+1);
  const sx=gx-x0, sy=gy-y0;
  const idx=(x,y)=> grid[y*HM_W + x];
  const top = lerp(idx(x0,y0), idx(x1,y0), sx);
  const bot = lerp(idx(x0,y1), idx(x1,y1), sx);
  const val = lerp(top, bot, sy);
  return gridMax>0 ? constrain(val/gridMax, 0, 1) : 0;
}

function quantile(sortedArr, q){
  if (!sortedArr.length) return 1;
  const pos = (sortedArr.length-1)*constrain(q,0,1);
  const base = floor(pos), rest = pos - base;
  return sortedArr[base] + (sortedArr[base+1]!==undefined ? rest*(sortedArr[base+1]-sortedArr[base]) : 0);
}

// Pantalla → coords dentro del disco
function toDisc(px,py,R){
  let vx=(px/width -0.5)*2*R*0.85;
  let vy=(py/height-0.5)*2*R*0.85;
  const len=Math.sqrt(vx*vx+vy*vy), cap=R*0.95;
  if (len>cap){ const s=cap/len; vx*=s; vy*=s; }
  return {x:R+vx, y:R+vy};
}

// —————————————————— GEOGRAFÍA (tierra = heat) + FADE/ZOOM ——————————————————
function buildPlanetFromRun(){
  // Semilla desde el recorrido
  let acc = 0;
  for (let i=0;i<pathPoints.length;i++){
    const p = pathPoints[i];
    acc = (acc ^ (floor(p.x)*73856093)) + (floor(p.y)*19349663);
  }
  const seed = floor((acc % 1e9 + 1e9) % 1e9);
  randomSeed(seed); noiseSeed(seed);

  // Preparar grids
  rebuildHeatSmoothed();
  rebuildHeatLand();

  // Umbral por percentil (con grid macro)
  const nz = heatLand.map(v => heatLandMax>0 ? v/heatLandMax : 0)
                     .filter(v => v>0).sort((a,b)=>a-b);
  const thr = nz.length ? quantile(nz, 1 - LAND_RATIO_TARGET) : 1;

  const R  = floor(min(width,height) * 0.34);
  const cx = width/2, cy = height/2;

  const pg = createGraphics(R*2, R*2); pg.pixelDensity(1); pg.colorMode(HSB,360,100,100,100);
  pg.background(0,0,0,0);

  // Colores base (aleatorios y contrastados por iteración)
  const base  = floor(random(0, 360));
  const delta = random(140, 220);
  const flip  = random() < 0.5;
  let landH  = (base) % 360;
  let waterH = (base + delta) % 360;
  if (flip){ const tmp = landH; landH = waterH; waterH = tmp; }

  // Disco: tierra donde heat macro > umbral (con perilla de grosor)
  pg.loadPixels();
  for (let y=-R; y<R; y++){
    for (let x=-R; x<R; x++){
      const rr = x*x + y*y; if (rr > R*R) continue;

      let heatN = sampleHeat01From(heatLand, heatLandMax, x, y, R);
      heatN = constrain(heatN * LAND_THICKNESS, 0, 1);     // perilla de grosor
      const m  = smoothstep(thr - FEATHER, thr + FEATHER, heatN);
      const isLand = m >= 0.5;

      // Luz esférica simple
      const N = p5.Vector.normalize(createVector( x/R, y/R, sqrt(max(0,1 - rr/(R*R))) ));
      const L = p5.Vector.normalize(createVector(-0.6,-0.5,0.62));
      const lambert = constrain( (N.x*L.x + N.y*L.y + N.z*L.z), 0.15, 1.0 );

      const h = isLand ? landH : waterH;
      const s = isLand ? (60 + random(0,8)) : (55 + random(0,8));
      const b = isLand ? 70 + 25*lambert : 75 + 20*lambert;

      const col = pg.color(h, s, b, 100);
      setPixelHSB(pg, x+R, y+R, col);
    }
  }
  pg.updatePixels();

  // Atmósfera/halo
  pg.push();
  for (let i=0;i<14;i++){
    const t=i/14; pg.noFill();
    pg.stroke((waterH+20)%360, 40, 100, 12*(1-t));
    pg.strokeWeight(9*(1-t));
    pg.circle(R, R, (R*2-3) + i*2);
  }
  pg.pop();

  // Hotspots dorados (áreas de alta estancia fina)
  const top = heatSmoothed.map((v,i)=>({v,i})).sort((a,b)=>b.v-a.v).slice(0,12).filter(e=>e.v>1e-6);
  pg.push(); pg.blendMode(ADD); pg.noStroke();
  for (const e of top){
    const gx = e.i % HM_W, gy = floor(e.i/HM_W);
    const xw = map(gx,0,HM_W-1, -R*0.9, R*0.9);
    const yw = map(gy,0,HM_H-1, -R*0.9, R*0.9);
    if (xw*xw + yw*yw <= R*R){
      const rad = map(e.v, 0, heatMaxSmooth, 10, 60);
      pg.fill(45, 90, 100, 50);  pg.circle(R+xw, R+yw, rad*0.5);
      pg.fill(20, 90, 100, 34);  pg.circle(R+xw, R+yw, rad);
    }
  }
  pg.pop();

  // Trazos del recorrido (polilínea real sobre el disco)
  pg.push(); pg.blendMode(ADD);
  pg.noFill(); pg.stroke(50, 30, 100, 85); pg.strokeWeight(2.2);
  pg.beginShape();
  for (let i=0;i<pathPoints.length;i++){
    const p = pathPoints[i];
    const q = toDisc(p.x, p.y, R);
    if (i>0){
      const qprev = toDisc(pathPoints[i-1].x, pathPoints[i-1].y, R);
      if (dist(q.x,q.y, qprev.x,qprev.y) > 40){ pg.endShape(); pg.beginShape(); }
    }
    pg.vertex(q.x, q.y);
  }
  pg.endShape();
  pg.pop();

  planet = { gfx: pg, R, cx, cy, seed };
  planetPhase = "IN";
  planetT0 = millis();
}

function drawPlanet(){
  // Fondo estelar leve
  push(); noStroke();
  for (let i=0;i<50;i++){
    fill(0,0,100, 10);
    circle(random(width), random(height), random(1,2));
  } pop();

  if (!planet){ buildPlanetFromRun(); }

  // Animación de aparición / salida
  let scaleF = 1, alphaF = 1;

  if (planetPhase === "IN"){
    const p = constrain((millis()-planetT0)/PLANET_IN_DUR, 0, 1);
    const e = easeOutCubic(p);
    scaleF = lerp(ZOOM_IN_START, ZOOM_IN_END, e);
    alphaF = e;
    if (p >= 1) planetPhase = "SHOW";
  } else if (planetPhase === "OUT"){
    const p = constrain((millis()-planetT0)/PLANET_OUT_DUR, 0, 1);
    const e = easeInCubic(p);
    scaleF = lerp(1.0, ZOOM_OUT_END, e);
    alphaF = 1.0 - e;
    if (p >= 1){ resetToReal(); return; }
  }

  // Dibujar con zoom y alpha
  push();
  translate(planet.cx, planet.cy);
  scale(scaleF);
  tint(255, 255*alphaF);
  image(planet.gfx, -planet.R, -planet.R);
  noTint();
  pop();

  // Mensaje inspirador (arriba) — aparece con el mismo alpha del planeta
  drawPlanetMessage(alphaF);

  // Velo para suavizar transición
  fill(0, 0, 0, 80*(1-alphaF)); noStroke(); rect(0,0,width,height);

  const hint = planetPhase==="SHOW" ? "Click: volver a cámara • " : "";
  hud(`Geografía: zonas de mayor estancia • ${hint}[S] guardar PNG • [R] reiniciar`);
}

function drawPlanetMessage(alphaF){
  if (!PLANET_MESSAGE) return;
  const pad = 18;
  const y = pad;
  const msg = PLANET_MESSAGE;

  push();
  textAlign(CENTER, TOP);
  textSize(20);
  let a = 85 * alphaF;                 // alpha 0..85
  const tw = textWidth(msg);
  const bx = width/2 - (tw/2) - 14;
  const bw = tw + 28;
  const bh = 34;

  noStroke();
  fill(0,0,0, a*0.65);                 // fondo translúcido
  rect(bx, y-6, bw, bh, 10);

  fill(0,0,100, a);                    // texto
  text(msg, width/2, y);
  pop();
}

function setPixelHSB(pg, x, y, c){
  const idx = 4*(y*pg.width + x);
  pg.pixels[idx+0] = red(c);
  pg.pixels[idx+1] = green(c);
  pg.pixels[idx+2] = blue(c);
  pg.pixels[idx+3] = alpha(c)*2.55;
}

// —————————————————— Clicks ——————————————————
function mousePressed(){
  userStartAudio(); // desbloquea audio en navegadores
  if (state === "COSMOS"){
    buildPlanetFromRun();
    state = "PLANETA";

    // crossfade: baja cosmos y entra música del planeta
    lowerBGM(0.0, 0.7);               // cosmos a 0 en 0.7s
    setTimeout(()=> stopBGM(), 800);  // apagar del todo luego
    startPlanetBGM();                 // fade-in del planeta
    return;
  }
  if (state === "PLANETA" && planetPhase === "SHOW"){
    planetPhase = "OUT";
    planetT0 = millis();
    stopPlanetBGM();                  // fade-out planeta
  }
}

function keyPressed(){
  userStartAudio();
  if (key==='M' || key==='m') useHand = !useHand;
  if (key==='R' || key==='r') resetToReal();
  if (key==='S' || key==='s') saveCanvas('geografia', 'png');
}
function touchStarted(){ userStartAudio(); return false; }

// —————————————————— Paletas, cámara, utilidades ——————————————————
function choosePalette(){
  const sets = [{h:[200,260,320,20,45]}, {h:[190,210,230]}, {h:[0,0,0,210]}];
  return random(sets);
}
function pickHue(){
  const hs = palette.h; let h = random(hs);
  if (random()<0.15) h = (h + random([-40,40]))%360;
  return (h+360)%360;
}
function updateHueVariance(h){ if (lastHue===null) lastHue=h; hueVar += abs(h-lastHue); lastHue=h; }

function analyzeCamera(){
  if (!video || video.width===0) return;
  video.loadPixels(); prevFrame.loadPixels();

  let sum=0, cx=0, cy=0, count=0;
  for (let y=0; y<video.height; y+=2){
    for (let x=0; x<video.width; x+=2){
      const idx=4*(y*video.width + x);
      const r=video.pixels[idx], g=video.pixels[idx+1], b=video.pixels[idx+2];
      const l=(0.299*r+0.587*g+0.114*b)/255; sum+=l;

      const pr=prevFrame.pixels[idx]||0, pg=prevFrame.pixels[idx+1]||0, pb=prevFrame.pixels[idx+2]||0;
      const diff=(abs(r-pr)+abs(g-pg)+abs(b-pb))/765;
      if (diff>0.18){ cx+=x; cy+=y; count++; }

      prevFrame.pixels[idx]=r; prevFrame.pixels[idx+1]=g; prevFrame.pixels[idx+2]=b; prevFrame.pixels[idx+3]=255;
    }
  }
  prevFrame.updatePixels();

  const avg = sum / ((video.width/2)*(video.height/2));
  darkEMA = 0.9*darkEMA + 0.1*avg;

  if (count>18){
    const vx=cx/count, vy=cy/count;
    motion.x = width - map(vx,0,video.width,0,width);
    motion.y = map(vy,0,video.height,0,height);
    motion.strength = constrain(map(count,18,(video.width*video.height)/6,0.15,1.0), 0.15, 1.0);
  } else { motion.x=null; motion.y=null; motion.strength=0; }
}

function hud(t){ fill(0,0,100,80); textAlign(CENTER,BOTTOM); textSize(14); text(t,width/2,height-16); }
function easeOutCubic(t){ return 1 - pow(1 - t, 3); }
function easeInCubic(t){ return t*t*t; }
function smoothstep(a, b, x){
  const t = constrain((x - a) / ((b - a) || 1e-9), 0, 1);
  return t * t * (3 - 2 * t);
}

// —————————————————— Audio helpers ——————————————————
function startBGM(){
  if (!bgm || bgmStarted) return;
  userStartAudio();
  if (!bgm.isPlaying()){
    bgm.setLoop(true);
    bgm.setVolume(0);
    bgm.play();
    bgm.setVolume(0.6, 1.5);   // fade-in a 0.6 en 1.5s
  }
  bgmStarted = true;
}
function lowerBGM(vol=0.25, ramp=0.8){
  if (bgm && bgm.isPlaying()) bgm.setVolume(vol, ramp);
}
function stopBGM(){
  if (bgm && bgm.isPlaying()){
    bgm.setVolume(0, 1.0);
    setTimeout(()=>{ if (bgm) bgm.stop(); }, 1000);
  }
  bgmStarted = false;
}
function startPlanetBGM(){
  if (!bgmPlanet || planetAudioStarted) return;
  userStartAudio();
  if (!bgmPlanet.isPlaying()){
    bgmPlanet.setLoop(true);
    bgmPlanet.setVolume(0);
    bgmPlanet.play();
    bgmPlanet.setVolume(0.55, 1.2); // fade-in planeta
  }
  planetAudioStarted = true;
}
function stopPlanetBGM(){
  if (bgmPlanet && bgmPlanet.isPlaying()){
    bgmPlanet.setVolume(0, 0.8);
    setTimeout(()=>{ if (bgmPlanet) bgmPlanet.stop(); }, 820);
  }
  planetAudioStarted = false;
}

```

<img width="860" height="762" alt="image" src="https://github.com/user-attachments/assets/409d1dd2-7322-45ce-9047-de3ba8f15d34" />

<img width="859" height="780" alt="image" src="https://github.com/user-attachments/assets/b2bab657-b658-4519-ac98-21ded02216a3" />

<img width="797" height="712" alt="image" src="https://github.com/user-attachments/assets/d29c7e08-ba07-4f82-9902-7521b0226fc8" />

<img width="800" height="742" alt="image" src="https://github.com/user-attachments/assets/79f2437d-96ca-4ddb-a6cb-e1b245359da4" />




## Justificación rúbrica


### 1) Investigación y experimentación : Excelente 5.0

Evidencias
- Ejemplo 1 — https://editor.p5js.org/sebastr008/sketches/pL3UAejlS  
  • Creación/desaparición: push al arreglo por frame, borrando de atrás para adelante con splice cuando lifespan < 0.  
  • Memoria: al eliminar la referencia, el GC libera el objeto, justifico el bucle inverso para no saltar índices del array particles.  
  • Experimentación: Levy flight (umbral 0.01), límite de velocidad y lifespan aleatorio, en donde ahora hay variaciones de transparencia y saltos.

- Ejemplo 2 — https://editor.p5js.org/sebastr008/sketches/taS91SqYR  
  • Creación/desaparición: múltiples emisores en el array emitters[], cada uno con su propio particles[], mismo borrado "inverso".  
  • Memoria: mismo criterio, encapsulado por emisor.  
  • Experimentación: mezcla de dirección al mouse con Levy.

- Ejemplo 3 — https://editor.p5js.org/sebastr008/sketches/mXNhoLO6a  
  • Creación/desaparición: un emisor, y hay polimorfismo de Particle a Confetti, borrado inverso.  
  • Memoria: GC tras splice  
  • Experimentación: herencia (Confetti extiende Particle) y resistencia del aire aplicada como fuerza opuesta a la velocidad.

- Ejemplo 4 — https://editor.p5js.org/sebastr008/sketches/PB7KJ4JIV  
  • Creación/desaparición: emitter.addParticle() y emitter.run() con borrado inverso.  
  • Memoria: variables de fuerzas globales reutilizadas para no crear vectores iguales cada frame.  
  • Experimentación: viento sinusoidal (windAmp * sin(theta)) inyectado al emisor, pruebas de amplitud y frecuencia visibles en la trayectoria.

- Ejemplo 5 — https://editor.p5js.org/sebastr008/sketches/jcP0cXrKe  
  • Creación/desaparición: patrón consistente (spawn + vida + borrado inverso).  
  • Memoria: igual.  
  • Experimentación: repulsor controlado con WASD y micro-oscilación angular por partícula.


las preguntas están respondidas y en cada ejemplo se añade una mejora usando conceptos de todas las unidades anteriores.

### 2) Intención y diseño

Evidencias
- Proceso conceptual: moodboard con palabras clave, combinaciones y síntesis en el concepto  
  “Cada parpadeo crea una dimensión maleable donde la presencia modela universos y variaciones sutiles producen grandes cambios.”
- Bocetos: se ve la referencia a portal (oscurecer cámara), el cosmos (sistema de partículas) y el resultado (forma que registra el viaje).
- Decisiones de diseño: Portal/umbral: tapar y destapar la cámara. Exploración: cosmos con glow, profundidad y repulsión ligada al gesto (sensación de estar viajando a través del cosmos). Sentido: El planeta que sale refleja estancia (heatmap -> “tierra/agua”), destaca zonas de atención (hotspots) y preserva el trazo real del recorrido. Audio: fade-in en cosmos y crossfade en la forma final para reforzar atmósfera y cierre.

Por qué es Excelente: hay relación directa del concepto a interacción y visuales, y evolución documentada desde las primeras ideas/limitaciones hasta una idea ya bien concreta

### 3) Aplicación técnica (código)

Evidencias
## Unidad 1 — Tipos de random
- randomSeed()/noiseSeed() para controlar la aleatoriedad por sesión.
- Colores aleatorios por iteración para “tierra/agua”.
Implementado en el proyecto: generación del planeta y el flujo de partículas usa semillas y paletas aleatorias.

## Unidad 2 — Motion 101
- Integración aceleración -> velocidad -> posición en cada partícula.
- Se usó fricción para estabilizar el movimiento.
Implementado en el proyecto: Partículas con fricción para mantener fluidez y control.

## Unidad 3 — Fuerzas y campos
- Atracción radial hacia el punto de fuga.
- Repulsión sensible al gesto (mouse/mano) con radio variable.
Implementado en el proyecto: combinación de campo radial + repulsión, base del “viaje” por el cosmos.

## Unidad 4 — Angular / sinusoidal / oscilatorio
- Ondulación sutil por partícula usando un ángulo propio (theta/omega) y sin(theta).
Implementado en el proyecto: oscilaciones que enriquecen el movimiento sin perder estabilidad.

## Unidad 5 — Sistema de partículas / Herencia / Polimorfismo
- Clase base de partícula y subclases con comportamiento/visual distintos.
- Emisor explícito que controla tasa y cantidad.
- Gestión de vida y memoria mediante lifespan y reciclaje de partículas.
Implementado en el proyecto: Particle base + tipos derivados, Emitter con tasa/techo, vida decreciente y reciclaje para evitar acumulación.

### 4) Calidad del artefacto final

Evidencias
- Transición de entrada al cosmos con fade-in y música ambiental.  
- Interacción: cubrir cámara para entrar; repulsión al gesto, sensación de atravesar el campo de partículas.  
- Forma final legible: zonas de mayor estancia visibles y trazo real del recorrido, posibilidad de conservar el resultado como imagen.  

### Resumen
1) Investigación y experimentación: respuestas claras en Ej. 1–5 y mejoras probadas. Nota: 5.0  
2) Intención y diseño: concepto definido y relación directa a interacción y forma. Nota: 5.0  
3) Aplicación técnica: contenidos de las unidades 1–5 aplicados correctamente. Nota: 5.0  
4) Calidad del artefacto final: resultado estable, legible y exportable. Nota: 5.0
































