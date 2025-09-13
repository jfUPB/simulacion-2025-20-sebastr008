# Evidencias de la unidad 5

## Actividad 2

### Ejemplo 1:

**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

La creación de partículas empieza por la creación del array "particles" en dónde se almacenarán las partículas a mostrar. La clase "particle" tiene un constructor en dónde se le define la posicion de la particula, su aceleración, su velocidad y por último su "lifespan" también hay métodos dentro de esta clase como el run() que se encarga de llamar a los métodos "auxiliares" por así decirlo (métodos para aplicar fuerzas, mostrar la partícula y actualizar la velocidad y la aceleración). La desaparición de las partículas está hecha de manera que en cuanto el lifespan de cierta partícula llega a 0, se le hace un splice a esa particula, sacandola del array y dandole paso a la siguiente partícula para que se genere.

**¿Cómo se gestiona la memoria?**

En esta simulación la memoria se gestiona con el array de particulas y el lifespan de cada partícula, cuando el lifespan de una particula es menor a 0, se le hace un splice, eliminando así la referencia de esa partícula y haciendo que la memoria no se vaya llenando cada vez más y más.

En cada frame genero una partícula nueva y actualizo las existentes. Cada partícula tiene un lifespan que decrece; cuando llega a cero, isDead() devuelve true y la elimino del arreglo iterando de atrás hacia adelante para no romper los índices. Al eliminar la referencia, el recolector de basura de JavaScript libera la memoria del objeto. Así gestiono creación, desaparición y memoria sin fugas.

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










