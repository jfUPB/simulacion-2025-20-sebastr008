# Unidad 1

## 🛠 Fase: Apply

### Actividad 8

Con esta obra generativa quise transmitir la sensación de pintar sobre el cielo, pero no con las manos, sino con el viento. Aquí, no eres quien dirige los trazos, solo quien les da alma. Puedes cambiar sus colores, su intensidad, su forma de moverse, pero al final, son ellos los que deciden hacia donde van, creando así una obra única con cada iteración.

```js
let zoff = 0;
let ball;
let movementMode = 0; // 0: Perlin, 1: Levy, 2: Normal
let colors = ["red", "green", "blue", "purple", "orange"];
let currentColor = 0; // Empezamos con el color rojo

function setup() {
  createCanvas(240, 240);
  ball = new Ball(width / 2, height / 2);
}

function draw() {
  background(220);
  
  loadPixels();

  let xoff = 0;
  for (let x = 0; x < width; x++) {
    let yoff = 0;
    for (let y = 0; y < height; y++) {
      let bright = map(noise(xoff, yoff, zoff), 0, 1, 0, 255);
      let index = (x + y * width) * 4;
      pixels[index + 0] = bright; 
      pixels[index + 1] = bright; 
      pixels[index + 2] = bright; 
      pixels[index + 3] = 200;    
      yoff += 0.01;
    }
    xoff += 0.01;
  }

    updatePixels();
  
  // Mover y dibujar la bola (deja un trazo)
  ball.move();
  ball.display();
  

  zoff += 0.01; 
}

function mousePressed() {
  // Cambiar modo de movimiento al hacer clic
  movementMode = (movementMode + 1) % 3;
  console.log("Modo actual:", ["Perlin", "Levy", "Normal"][movementMode]);
}

function keyPressed() {
  if (key === 'q' || key === 'Q') {
    // Cambiar color con la tecla "Q"
    currentColor = (currentColor + 1) % colors.length;
  }
  
  if (key === '+' || key === '=') {
    // Aumentar tamaño de la bola
    ball.size += 2;
  } 
  
  if (key === '-') {
    // Disminuir tamaño de la bola
    ball.size = max(2, ball.size - 2);
  }
}

class Ball {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = createVector(0, 0);
    this.size = 20; // Tamaño inicial de la bola
    this.perlinOffsetX = random(1000);
    this.perlinOffsetY = random(1000);
    this.path = []; // Guardar los puntos por donde pasa la bola
  }

  move() {
    let velocity;

    // Cambio de movimiento según el modo
    switch (movementMode) {
      case 0: // Perlin noise
        let dx = map(noise(this.perlinOffsetX), 0, 1, -2, 2);
        let dy = map(noise(this.perlinOffsetY), 0, 1, -2, 2);
        velocity = createVector(dx, dy);
        this.perlinOffsetX += 0.01;
        this.perlinOffsetY += 0.01;
        break;

      case 1: // Levy flight
        let angle = random(TWO_PI);
        let stepSize = this.levy();
        velocity = p5.Vector.fromAngle(angle).mult(stepSize);
        break;

      case 2: // Gaussian/normal
        let nx = randomGaussian() * 2;
        let ny = randomGaussian() * 2;
        velocity = createVector(nx, ny);
        break;
    }

    // Actualizar posición de la bola
    this.pos.add(velocity);
    this.pos.x = constrain(this.pos.x, 0, width);
    this.pos.y = constrain(this.pos.y, 0, height);

    // Agregar la posición, color y tamaño actual al camino (path)
    this.path.push({ 
      pos: this.pos.copy(), 
      color: colors[currentColor], 
      size: this.size 
    });

    // Limitar el tamaño del camino para evitar sobrecarga de memoria
    if (this.path.length > 500) {
      this.path.shift(); // Elimina el primer punto para mantener el tamaño del array
    }
  }

  levy() {
    let beta = 0.5; // Parámetro para el comportamiento de Levy
    let u = randomGaussian() * 0.5;
    let v = randomGaussian();
    let step = u / pow(abs(v), 1 / beta);
    return constrain(step * 10, 0, 50); // Limita el tamaño del paso
  }

 display() {
  // Dibujar la bola actual
  fill(colors[currentColor]);
  noStroke();
  ellipse(this.pos.x, this.pos.y, this.size, this.size);

  // Dibujar el trazo con bordes difuminados desde el inicio
  for (let i = 0; i < this.path.length; i++) {
    let p = this.path[i];

    // Color con transparencia constante (puedes ajustar a gusto)
    let c = color(p.color);
    c.setAlpha(50); // 0 (invisible) a 255 (opaco) — más bajo = más suave

    noStroke();
    fill(c);
    for (let r = p.size * 2; r > 0; r -= 2) {
  let alpha = map(r, p.size * 2, 0, 0, 50); // Ajusta el alpha según el tamaño
  let c = color(p.color);
  c.setAlpha(alpha);
  noStroke();
  fill(c);
  ellipse(p.pos.x, p.pos.y, r);
}

  }
}


}

```

[Link Proyecto](https://editor.p5js.org/sebastr008/sketches/RToTThZua)

<img width="257" height="251" alt="image" src="https://github.com/user-attachments/assets/5c1b4dd6-ae3f-4818-b468-2e3bbd1e3a0e" />
