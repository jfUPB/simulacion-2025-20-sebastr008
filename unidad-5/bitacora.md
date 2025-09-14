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


















