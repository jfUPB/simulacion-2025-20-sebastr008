# Evidencias de la unidad 4

## Explicación conceptual de la obra

* ¿Qué concepto de la unidad 4 y cómo lo aplicaste en la obra?
> La unidad 4 habla de movimientos angulares y oscilatorios. En mi obra, cada "orbiter" usa un oscilador que hace variar su ángulo alrededor del círculo y al mismo tiempo “respira” hacia adentro y hacia afuera. Eso lo resolví con coordenadas polares (p5.Vector.fromAngle(theta).mult(r)), aplicando directamente lo de oscilación armónica: amplitud, frecuencia y fase

* ¿Qué concepto de la unidad 3 y cómo lo aplicaste en la obra?
> Aunque no se usa gravedad en mi proyecto, el audio se "modela" como una fuerza externa que modifica la amplitud y velocidad de los osciladores. El radio de cada orbiter está controlado por un “resorte” virtual que lo mantiene cerca de su órbita base (r0 ± rango), simulando un sistema físico estable

* ¿Qué concepto de la unidad 2 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> En mi proyecto, la posición de cada orbiter se calcula con vectores (p5.Vector.fromAngle(theta).mult(r).add(center)). El oscilador también usa p5.Vector para almacenar ángulo, velocidad y amplitud. Además, el esquema Motion 101 se cumple porque cada frame el ángulo se actualiza sumando su velocidad angular, lo que define el nuevo estado de movimiento

* ¿Qué concepto de la unidad 1 y cómo lo aplicaste en la obra?
> Utilicé randomGaussian para asignar tamaños iniciales distintos a los orbiters, generando una variación natural. También usé noise para cambiar suavemente el color y el brillo de cada orbiter, evitando cambios bruscos y visuales más fluidos

## ¿Cómo resolviste la interacción?
> La interacción se da con el mouse y el audio en tiempo real. El usuario puede hacer clic sobre los orbiters para activarlos, y cada color representa una banda del ecualizador (morado = bajos, verde = medios, naranja = agudos). El halo que aparece alrededor refleja la ganancia que recibe esa banda. El audio, ya sea cargado como archivo o desde el micrófono, va modulando los movimientos y colores

## Enlace a la obra en el editor de p5.js

[Aquí está mi obra](https://editor.p5js.org/sebastr008/sketches/uJNyo-bMC)

## Código de la obra 

``` js
// Orbital Music Visualizer — FIX: oscilador estable (sin explosión), límites de radio, EQ multi-select
// U1: randomGaussian + noise | U2: p5.Vector | U3: Oscillator (NOC)

let fft, amp, audio, mic, useMic=false, playing=false;
let orbitalLayers = [];
let time = 0;

// ===== EQ (3 bandas) =====
let eq; // lows, mids, highs

// ===== Análisis =====
let spectrum = [];
let bassEnergy, midEnergy, trebleEnergy;

// ===== Capas / estética =====
const NUM_LAYERS = 3;                      // 0: morado(bajos) 1: verde(medios) 2: naranja(altos)
const ORBITERS_PER_LAYER = [8, 10, 12];
const LAYER_RADII = [150, 220, 290];
const LAYER_COLORS = [280, 120, 30];       // HSB approx
let colorOffset = 0;

// ===== Parámetros de estabilidad =====
const AMP_ANG_MIN = 0.10;   // amplitud angular base (rad)
const AMP_ANG_MAX = 0.30;   // (valor de arranque aleatorio)
const AMP_RAD_MIN = 6;      // amplitud radial base (px)
const AMP_RAD_MAX = 18;

const VEL_ANG_MIN = 0.010;  // vel angular base (rad/frame)
const VEL_ANG_MAX = 0.022;
const VEL_RAD_MIN = 0.006;  // vel radial base
const VEL_RAD_MAX = 0.016;

// Modulación por audio (estables, no acumulativas)
const ANG_VEL_COEF = 0.60;   // +60% máx por audio
const RAD_VEL_COEF = 0.40;
const ANG_AMP_COEF = 1.20;   // +120% máx por audio
const RAD_AMP_COEF = 0.80;

const AMP_LERP = 0.15;       // suavizado de amplitud
const R_RANGE_MAX = 28;      // tope de respiración radial (px)

// ===== Oscillator (NOC) estable =====
class Oscillator {
  constructor(ampX, ampY, velX, velY) {
    this.angle = createVector(0, 0);
    // bases inmutables
    this.baseAmp = createVector(ampX, ampY);
    this.baseVel = createVector(velX, velY);
    // estados suavizados
    this.amp = this.baseAmp.copy();
  }
  update(audio){ // audio en 0..1
    // vel efectivas (no acumular multiplicando la base, se calculan cada frame)
    const vx = this.baseVel.x * (1 + ANG_VEL_COEF * audio);
    const vy = this.baseVel.y * (1 + RAD_VEL_COEF * audio);
    this.angle.x += vx;
    this.angle.y += vy;

    // amplitudes target (desde base → base*(1 + coef*audio))
    const targetAx = this.baseAmp.x * (1 + ANG_AMP_COEF * audio);
    const targetAy = this.baseAmp.y * (1 + RAD_AMP_COEF * audio);
    this.amp.x = lerp(this.amp.x, targetAx, AMP_LERP);
    this.amp.y = lerp(this.amp.y, targetAy, AMP_LERP);
  }
  value(){
    return createVector(
      sin(this.angle.x) * this.amp.x,   // offset angular (rad)
      sin(this.angle.y) * this.amp.y    // offset radial (px)
    );
  }
}

class Orbiter {
  constructor(layer, index, radius) {
    this.layer = layer;
    this.index = index;
    this.r0 = radius; // radio base

    // ángulo base en el círculo
    this.baseAngle = map(index, 0, ORBITERS_PER_LAYER[layer], 0, TWO_PI);

    // (U3) oscilador NOC: x = ángulo, y = radio
    const ampX = random(AMP_ANG_MIN, AMP_ANG_MAX);
    const ampY = random(AMP_RAD_MIN, AMP_RAD_MAX);
    const velX = random(VEL_ANG_MIN, VEL_ANG_MAX);
    const velY = random(VEL_RAD_MIN, VEL_RAD_MAX);
    this.osc = new Oscillator(ampX, ampY, velX, velY);

    // (U2) posición como vector
    this.pos = createVector(width/2, height/2)
      .add(p5.Vector.fromAngle(this.baseAngle).mult(this.r0));

    // (U1) tamaño y ruido
    this.size = constrain(randomGaussian(14, 3), 10, 20);
    this.noiseOffset = random(1000);

    // audio local suavizado
    this.audioResponse = 0;

    // color por capa
    this.hue = LAYER_COLORS[layer];
    this.isSelected = false;
  }

  readAudioLocal(audioData){
    const bandIndex = floor(map(this.index, 0, ORBITERS_PER_LAYER[this.layer], 10, 200));
    return (audioData[bandIndex] || 0) / 255.0;
  }

  update(audioData) {
    // suavizado de respuesta
    const targetResp = this.readAudioLocal(audioData);
    this.audioResponse = lerp(this.audioResponse, targetResp, 0.12);

    // actualizar oscilador con audio (SIN multiplicaciones acumulativas)
    this.osc.update(this.audioResponse);

    // offsets
    const o = this.osc.value();
    const theta = this.baseAngle + o.x;

    // radio con tope duro alrededor de r0
    let r = this.r0 + o.y;
    const rMin = this.r0 - R_RANGE_MAX;
    const rMax = this.r0 + R_RANGE_MAX;
    r = constrain(r, rMin, rMax);

    // (U2) posición
    this.pos = createVector(width/2, height/2)
      .add(p5.Vector.fromAngle(theta).mult(r));

    this.noiseOffset += 0.01;
  }

  currentHaloGain(){
    const displaySize = this.size * (1 + this.audioResponse * 0.9);
    const haloSize = displaySize * (2 + this.audioResponse * 3);
    return map(haloSize, this.size, this.size * 12, 0, 12, true);
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);

    const colorNoise = noise(this.noiseOffset * 0.1, colorOffset);
    const hue = (this.hue + colorNoise * 40) % 360;
    const sat = map(this.audioResponse, 0, 1, 60, 100);
    const bright = map(this.audioResponse, 0, 1, 70, 100);

    colorMode(HSB, 360, 100, 100);

    if (this.isSelected) {
      stroke(60, 100, 100);
      strokeWeight(3);
      noFill();
      circle(0, 0, this.size * 2.5);
    }

    noStroke();
    fill(hue, sat, bright);
    const displaySize = this.size * (1 + this.audioResponse * 0.9);
    circle(0, 0, displaySize);

    fill(hue, sat * 0.5, 100, 0.7);
    circle(0, 0, displaySize * 0.6);

    fill(0, 0, 100);
    circle(0, 0, 4);

    if (this.audioResponse > 0.2) {
      noFill();
      stroke(hue, sat, 100, 70);
      strokeWeight(2);
      const haloSize = displaySize * (2 + this.audioResponse * 3);
      circle(0, 0, haloSize);
    }

    colorMode(RGB);
    pop();
  }

  contains(mx, my){
    return dist(mx, my, this.pos.x, this.pos.y) < this.size * 1.5;
  }
}

function setup() {
  createCanvas(900, 600);

  fft = new p5.FFT(0.8, 512);
  amp = new p5.Amplitude();
  mic = new p5.AudioIn();

  eq = new p5.EQ(3);

  // Capas
  for (let layer = 0; layer < NUM_LAYERS; layer++) {
    orbitalLayers[layer] = [];
    for (let i = 0; i < ORBITERS_PER_LAYER[layer]; i++) {
      orbitalLayers[layer].push(new Orbiter(layer, i, LAYER_RADII[layer]));
    }
  }

  background(10, 15, 25);
}

function draw() {
  background(10, 15, 25, 60);

  time++;
  colorOffset += 0.01;

  spectrum = fft.analyze();
  bassEnergy   = fft.getEnergy("bass")   / 255;
  midEnergy    = fft.getEnergy("mid")    / 255;
  trebleEnergy = fft.getEnergy("treble") / 255;

  // 1) Anillos guía
  drawOrbitalRings();

  // 2) Sine wave a lo ancho
  drawFullWidthSine();

  // 3) Orbiters
  for (let layer = 0; layer < NUM_LAYERS; layer++) {
    for (let orbiter of orbitalLayers[layer]) {
      orbiter.update(spectrum);
      orbiter.display();
    }
  }

  // 4) EQ multi-select
  applyMultiSelectEQ();
}

// ---- Anillos
function drawOrbitalRings() {
  push();
  translate(width/2, height/2);
  noFill();
  stroke(120, 150);
  strokeWeight(1);
  for (let r of LAYER_RADII) circle(0, 0, r * 2);
  pop();
}

// ---- Sine wave a lo ancho
function drawFullWidthSine() {
  push();
  translate(0, height / 2);

  const ampLevel = amp.getLevel() * 300;
  const waveLength = 200;

  beginShape();
  noFill();
  strokeWeight(3);

  for (let x = 0; x <= width; x += 10) {
    const ang = (x / waveLength) * TWO_PI + frameCount * 0.05;
    const y = sin(ang) * ampLevel;

    colorMode(HSB, 360, 100, 100);
    const hue = (map(x, 0, width, 0, 360) + frameCount * 2) % 360;
    stroke(hue, 90, 100);
    vertex(x, y);
  }
  endShape();

  colorMode(RGB);
  pop();
}

// ---- EQ multi-select: suma por banda (tope +12 dB)
function applyMultiSelectEQ() {
  let low = 0, mid = 0, high = 0;

  for (let layer = 0; layer < NUM_LAYERS; layer++) {
    for (let o of orbitalLayers[layer]) {
      if (!o.isSelected) continue;
      const gain = o.currentHaloGain(); // 0..12
      if (o.hue > 200)       low  += gain;  // morado → lows
      else if (o.hue > 60)   mid  += gain;  // verde  → mids
      else                   high += gain;  // naranja→ highs
    }
  }

  low  = constrain(low,  0, 12);
  mid  = constrain(mid,  0, 12);
  high = constrain(high, 0, 12);

  if (low === 0 && mid === 0 && high === 0) setEQ(0,0,0);
  else setEQ(low, mid, high);
}

function setEQ(lowGain, midGain, highGain) {
  eq.bands[0].gain(lowGain);
  eq.bands[1].gain(midGain);
  eq.bands[2].gain(highGain);
}

// ---- Selección múltiple (toggle)
function mousePressed() {
  let clickedAny = false;
  for (let layer = 0; layer < NUM_LAYERS; layer++) {
    for (let orbiter of orbitalLayers[layer]) {
      if (orbiter.contains(mouseX, mouseY)) {
        orbiter.isSelected = !orbiter.isSelected;
        clickedAny = true;
        break;
      }
    }
    if (clickedAny) break;
  }
  if (!clickedAny) {
    for (let layer = 0; layer < NUM_LAYERS; layer++) {
      for (let orbiter of orbitalLayers[layer]) orbiter.isSelected = false;
    }
  }
}

// ---- Carga de audio (usa tu UI de index.html)
function loadAudioFile(file) {
  if (audio) { audio.stop(); audio.disconnect(); }
  audio = loadSound(URL.createObjectURL(file), () => {
    useMic = false;
    audio.disconnect();
    eq.process(audio);   // pasa por EQ
    fft.setInput(eq);    // analizamos post-EQ
    amp.setInput(eq);    // la sine responde post-EQ
    audio.play();
    playing = true;
  });
}

function keyPressed() {
  if (key === ' ') {
    if (audio) {
      if (audio.isPlaying()) { audio.pause(); playing = false; }
      else { audio.play(); playing = true; }
    }
  }
}

```

## Captura de pantalla representativa

<img width="838" height="752" alt="image" src="https://github.com/user-attachments/assets/a6fa55f5-9531-4c00-a815-0364ab44bf90" />






