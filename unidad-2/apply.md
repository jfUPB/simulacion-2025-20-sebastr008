# Unidad 2


## 🛠 Fase: Apply


### Concepto

Esta obra generativa te sumerge en un mundo mágico donde te conviertes en un hechicero. A través de un gesto simple como abrir la mano eres capaz de lanzar una esfera de poder en la dirección que apuntas, como si dominaras fuerzas invisibles. Puedes sentir que controlas y que lanzas una energía viva.

### Aplicación del Motion 101 y que algoritmos de aceleración usé

En esta aplicación usé el motion 101 con las aceleraciones que aprendimos en esta unidad y también lo combiné con conceptos vistos en la unidad anterior como el ruido Perlin. En este proyecto se usan vectores de posición, aceleración y velocidad para modificar la posición de todas las partículas. Un tipo de aceleración que hay es muy parecido al de seguir al mouse y esto lo utitilcé para que las particulas siguieran la mano detectada por ml5, luego al momento de que el programa detecta que se abrió la mano, utilizó una fuerza hecha con ruido perlin que posteriormente se le pasa a la aceleración, generando así una sensación mucho más fluida de que efectivamente lanzaste las partículas.

### Código

```js
let video;
let handpose;
let predictions = [];
let drops = [];
let interp = 0;
let r;
let b;
let blending;
let blendDirect = 1;
let accelMode ="perlin";

const NUM_DROPS = 200;

let prevHandState = "unknown";
let lastChangeFrame = 0;
let fliesAway = false;

let handStateHistory = [];

function setup() {
  createCanvas(640, 480);
  video = createCapture(VIDEO);
  video.size(width, height);
  video.hide();

  handpose = ml5.handpose(video, { flipHorizontal: true }, () => {
    console.log("handPose listo");
  });
  handpose.on("predict", (results) => {
    predictions = results;
  });

  for (let i = 0; i < NUM_DROPS; i++) {
    drops.push(new Drop());
  }
}

function draw() {
  background(0);
  image(video, 0, 0, width, height);

  // Espejo
  translate(width, 0);
  scale(-1, 1);

  r = color(255, 0, 0);
  b = color(0, 0, 255);
  blending = lerpColor(r, b, interp);

  let forcePoint;

  if (predictions.length > 0) {
    let hand = predictions[0];

    //drawLandmarks(hand);

    let currentState = isHandClosed(hand) ? "closed" : "open";

    // Histéresis: suavizar la detección con historial
    handStateHistory.push(currentState);
    if (handStateHistory.length > 10) handStateHistory.shift();

    let closedCount = handStateHistory.filter((s) => s === "closed").length;
    let openCount = handStateHistory.filter((s) => s === "open").length;

    let smoothState = closedCount > openCount ? "closed" : "open";

    if (prevHandState !== smoothState) {
      let framesSinceLastChange = frameCount - lastChangeFrame;

      if (
        prevHandState === "closed" &&
        smoothState === "open" &&
        framesSinceLastChange < 30
      ) {
        fliesAway = true;
        lastChangeFrame = frameCount;
        console.log("¡Gesto de abrir detectado! Partículas vuelan.");
      }

      lastChangeFrame = frameCount;
      prevHandState = smoothState;
    }

    // Centro de la mano para aplicar fuerzas
    const topLeft = hand.boundingBox.topLeft;
    const bottomRight = hand.boundingBox.bottomRight;
    const cx = (topLeft[0] + bottomRight[0]) / 2;
    const cy = (topLeft[1] + bottomRight[1]) / 2;
    forcePoint = createVector(cx, cy);
  }

  // Apagar efecto después de 1 segundo
  if (fliesAway && frameCount - lastChangeFrame > 60) {
    fliesAway = false;
  }

  for (let d of drops) {
    if (!fliesAway && forcePoint) d.applyHandForce(forcePoint);
    d.update();
    d.show(blending);
  }

  interp += 0.01 * blendDirect;
  if (interp <= 0 || interp >= 1) blendDirect *= -1;
}

// Dibujar puntos y números
/*
function drawLandmarks(hand) {
  stroke(0, 255, 0);
  strokeWeight(4);
  noFill();

  for (let i = 0; i < hand.landmarks.length; i++) {
    const [x, y, z] = hand.landmarks[i];
    ellipse(x, y, 8);
    noStroke();
    fill(255);
    textSize(12);
    text(i, x + 5, y - 5);
    stroke(0, 255, 0);
  }
}

*/

// Detección robusta de mano cerrada
function isHandClosed(hand) {
  const fingers = [
    [4, 2],   // Pulgar
    [8, 5],   // Índice
    [12, 9],  // Medio
    [16, 13], // Anular
    [20, 17], // Meñique
  ];

  let wrist = createVector(...hand.landmarks[0]);
  let middleBase = createVector(...hand.landmarks[9]);
  let handScale = wrist.dist(middleBase);
  let threshold = handScale * 0.55;

  let closedFingers = 0;

  for (let [tipIdx, baseIdx] of fingers) {
    let tip = createVector(...hand.landmarks[tipIdx]);
    let base = createVector(...hand.landmarks[baseIdx]);
    let dist = tip.dist(base);
    if (dist < threshold) closedFingers++;
  }

  return closedFingers >= 3;
}

// Clase de partícula
class Drop {
  constructor() {
    this.pos = createVector(random(width), random(-500, 0));
    this.vel = createVector(0, random(2, 5));
    this.acc = createVector();
    this.len = random(10);
    this.noiseOffsetX = random(10);
    this.noiseOffsetY = random(10);
  }

  applyHandForce(pt) {
    let dir = p5.Vector.sub(pt, this.pos);
    let dist = dir.mag();
    if (dist < 50) {
      dir.setMag(map(dist, 0, 50, -2, -0.1)); // repele
    } else {
      dir.setMag(0.25); // atrae levemente
    }
    this.acc.add(dir);
  }
  
  applyPerlinForce(time) {
  let noiseX = noise(this.noiseOffsetX + time * 0.01);
  let noiseY = noise(this.noiseOffsetY + time * 0.01);
  let angle = noiseX * TWO_PI * 2;
  let mag = noiseY * 1.5;

  let force = p5.Vector.fromAngle(angle).mult(mag);
  this.acc.add(force);
}

  update() {
    if (fliesAway) {
      /*let randomForce = p5.Vector.random2D().mult(random(2, 5));
      this.acc.add(randomForce);*/
      this.applyPerlinForce(frameCount);
    }
    
      
    
    if (accelMode) {
    
    }
    

    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.vel.mult(0.98);

    if (this.pos.y > height) {
      this.pos.y = random(-200, 0);
      this.vel.y = random(2, 5);
    }
  }

  show(c) {
    stroke(c);
    strokeWeight(5);
    line(this.pos.x, this.pos.y, this.pos.x, this.pos.y + this.len);
  }
}

```


[LINK CÓDIGO](https://editor.p5js.org/sebastr008/sketches/H5gWBGtH7)

<img width="1836" height="897" alt="image" src="https://github.com/user-attachments/assets/ab6a9905-2b21-4f68-8112-9d6faab86051" />

