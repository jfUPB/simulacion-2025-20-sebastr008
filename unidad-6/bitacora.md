# Evidencias de la unidad 6

## Actividad #1


<img width="742" height="743" alt="image" src="https://github.com/user-attachments/assets/cf2e4387-d0e8-414e-b503-203c85c106fc" />

 


Me llamó la atención esta imagen principalmente porque nunca había visto una generación de trazos tan partícular, usualmente se ven trazos con curvas bastante redondeadas, y transiciones en su cambio de dirección bastante suaves, aparte, no todos los trazos tienen el mismo grosor, unos son bastante gruesos y otros bastante finos, normalmente veo trazos bastante uniformes respecto a su grosor y también con cambios de dirección más "curveados", por eso me llamó bastante la atención esta obra, aparte de que me gustó bastante el resultado final.

---

<img width="907" height="914" alt="image" src="https://github.com/user-attachments/assets/192c7126-8999-4dd8-9476-2df70a103602" />

Lo que más me llamó la atención de esta obra sin embargo es el resultado final, los colores y las formas son bastante bonitas; también me llamó bastante la atención y me llenó de curiosidad como hizo el campo de flujo para generar esta imagen tan partícular, parecen paraguas que repelen la lluvia o incluso medusas. Un resultado bastante interesante.


## Actividad 2:

**¿Qué es una fuerza de dirección?**

La steering force es la corrección que se le aplica a la velocidad de un objeto para que se acerque a una velocidad deseada. Primero se define un vector de deseo que apunta al objetivo, cuya magnitud no supera la velocidad máxima que puede llefar el objeto. Luego se calcula la dirección restando el vector de deseo con la velocidad y para que no sea tan brusco el movimiento se limita este vector, así el objeto no salta ni cambia bruscamente, sino que gira y ajusta su trayectoria de manera más suave.

**¿Qué diferencia tiene este tipo de fuerza con las que ya hemos estudiado en el contexto de la simulación de agentes?**

Antes se trabajaban con fuerzas del entorno como la gravedad, el viento o el rozamiento. Esas fuerzas empujan a los objetos sin intención y afectan por igual a todos los objetos cuando se crean. La steering force es distinta porque nace del objetivo del agente, este le indica hacia dónde quiere ir y cuánto debe corregir su velocidad.

**¿Qué relación tiene la steering force con Craig Reynolds y su trabajo en simulación de comportamiento animal?**

La relación que vi es que el movimiento colectivo puede verse natural si cada agente aplica tres comportamientos locales. Separación para no chocar. Alineación para apuntar en direcciones parecidas. Cohesión para mantenerse cerca. Cada uno genera una steering force, se les da pesos, las fuerzas se suman, se "recorta" el resultado para no pasar los límites de fuerza y de velocidad y se les aplica esa fuerza como aceleración en cada frame y de ahí aparece un movimiento que se siente bastante natural

## Actividad 3:

**Explica brevemente la estructura de datos usada para el campo de flujo y cómo se generan sus vectores.**

El flowfield se almacena en un array 2D llamado “field” que cubre todo el lienzo en celdas del tamaño que marque la “resolution”. En cada casilla guardo un vector 2D. Ese vector sale de un ángulo generado con ruido suave en las coordenadas de la celda y lo paso a dirección con coseno y seno (o p5.Vector.fromAngle), si quiero que el campo cambie en el tiempo, avanzo un “z” en el ruido y los vectores se actualizan de forma pareja frame a frame.

**Describe con tus palabras cómo un agente utiliza el campo para calcular su fuerza de dirección**

En cada update() el agente pregunta qué vector le toca con flow.lookup(this.position), lo ajusta a su velocidad objetivo con desired.setMag(this.maxspeed) y arma la fuerza de giro como la diferencia entre ese “desired” y su velocidad actual. A esa fuerza le pongo un tope con this.maxforce para evitar volantazos y recién ahí la aplico. Después integro normal: acumulo aceleración, actualizo la velocidad, muevo la posición y limpio la aceleración para el siguiente ciclo.

**Lista los parámetros clave identificados (resolución, maxspeed, maxforce)**

- resolution: define cuántas columnas/filas tiene el “field” y el nivel de detalle del flujo.

- noiseScale / inc: escala del ruido usado para generar el ángulo por celda.

- zOffset / zSpeed: sirve para que el campo “se anime” de forma suave

- maxspeed (agente): velocidad objetivo a la que alineo el vector “desired” que viene del campo.

- maxforce (agente): tope de la fuerza de giro, controla qué tan rápido puede corregir rumbo sin volantazos.

- lookup (modo de lectura): cómo se lee el vector de la malla en una posición

- vehicleCount: cantidad de agentes

**Modificación**

La modificación que le hice a la forma en la que se generan los vectores de campo de flujo consiste en restringir la dirección que puede tomar cada vector a 8 direcciones posibles, como la matemática no es mi mayor fuerte, le explique a chatgpt como funcionaba la función "init()" de la clase "flowfield" y le dije que quería que me restringiera el ángulo posible de cada vector a 8 posibles direcciones. En “setup()” también cambié la resolution de 20 a 8 para que el campo tenga muchas más celdas en pantalla y esos cambios se vean más seguido. Por último, en el constructor de “Vehicle” baje considerablemente el random de “maxspeed” y “maxforce” lo aumenté para que el agente se mueva mucho más lento pero tenga fuerza suficiente para cambiar su dirección de una cuando pisa una celda con dirección distinta, haciendo así que las trayectorias dejen de ser tan suaves y aparecen quiebres visibles cuando cruzan de celda, también se forman como carriles y los grupos tienden a alinearse en las mismas direcciones.

<img width="629" height="228" alt="image" src="https://github.com/user-attachments/assets/beab2211-bdec-4332-8abe-04f43dbd61ae" />


Codigo que alteré:

```js
createCanvas(640, 240);
  // Make a new flow field with "resolution" of 16
  flowfield = new FlowField(8);
  // Make a whole bunch of vehicles with random maxspeed and maxforce values
  for (let i = 0; i < 120; i++) {
    vehicles.push(
      new Vehicle(random(width), random(height), random(.5, 2), random(0.5, 0.10))
    );
  }
}
```

```js
 init() {
    // Reseed noise for a new flow field each time
    noiseSeed(random(10000));
    let xoff = 0;
    let inc = 0.35;                 
    let sector = TWO_PI / 8.0;      
    for (let i = 0; i < this.cols; i++) {
      let yoff = 0;
      for (let j = 0; j < this.rows; j++) {
        //{.code-wide} In this example, use Perlin noise to create the vectors.
        let angle = map(noise(xoff, yoff), 0, 1, 0, TWO_PI);
        angle = floor(angle / sector) * sector + sector * 0.5;
        this.field[i][j] = p5.Vector.fromAngle(angle);
        
        yoff += inc;
      }
      xoff += inc;
    }
  }
```

```js
flowfield = new FlowField(8);
```

## Actividad 4

La “Separación” se usa para que no se amontonen los agentes. Se recorre a los vecinos y, si alguno cae dentro de “desiredSeparation”, se calcula un vector desde ese vecino hacia la partícula, se normaliza, se escala más si está muy cerca, se promedia, se ajusta a maxspeed y se hace el steering (desired - velocity) limitado por maxforce. Así se los va corriendo un poco, no de golpe.

La “Alineación” se usa para que apunten parecido los agentes. Se toman las velocidades de los vecinos dentro de “neighborDist”, se promedian, se ajustan a maxspeed y se calcula el steering igual, limitándolo por maxforce. Con eso se va copiando el rumbo y el grupo se ordena sin perder lo que ya traía.

La “Cohesión” se usa para que el grupo no se rompa. Se promedian posiciones para sacar un centro y se hace un “seek” hacia ese punto con la misma receta: setMag(maxspeed), desired - velocity y se limita usando maxforce. Así se vuelve un poco al centro de forma suave.

- desiredSeparation: distancia mínima para que no se amontonen.

- neighborDist (o radios por regla: alignRadius, cohesionRadius): hasta dónde “ve” a los vecinos.

- wSep, wAlign, wCoh: pesos con los que se mezcla cada fuerza en applyForce().

- maxspeed: velocidad objetivo del boid (a lo que se ajusta el desired).

- maxforce: tope de la fuerza del steer

- fov (ángulo de visión): limita qué vecinos cuenta.

Modifique la cohesión y la separación, puse la cohesión en 0 y la separación la empecé a multiplicar por 3 generando así que ahora los boids no estén casi juntos si no que ahora parece que cada uno va por su lado, lo cuál tiene sentido que pasé ya que si le quite su cohesión que es lo que hace que se mantengan más juntos, y le aumento su separación, este comportamiento es esperado

```js
flock(boids) {
    let sep = this.separate(boids); // Separation
    let ali = this.align(boids); // Alignment
    let coh = this.cohere(boids); // Cohesion
    // Arbitrarily weight these forces
    sep.mult(3);
    ali.mult(1.0);
    coh.mult(0);
    // Add the force vectors to acceleration
    this.applyForce(sep);
    this.applyForce(ali);
    this.applyForce(coh);
  }
```

<img width="623" height="230" alt="image" src="https://github.com/user-attachments/assets/97ce243a-dbc2-41d8-a583-aae55a77c095" />


Ahora modifiqué el radio de percepción y lo puse en 1, y me salio un resultado parecido a cuando quite la cohesión y aumenté la separación, ahora los boids están separados pero no se mueven tanto, si no que están un poco más estáticos.

```js
let neighborDistance = 1;
    let sum = createVector(0, 0); // Start with empty vector to accumulate all locations
    let count = 0;
    for (let i = 0; i < boids.length; i++) {
      let d = p5.Vector.dist(this.position, boids[i].position);
      if (d > 0 && d < neighborDistance) {
        sum.add(boids[i].position); // Add location
        count++;
      }
    }
```

<img width="632" height="250" alt="image" src="https://github.com/user-attachments/assets/0b7671e1-7f48-4877-8675-add065603c39" />

https://github.com/user-attachments/assets/eaf27945-1068-4691-8da6-b717472115cf

---

## Actividad 5

**Conceptualización**

El concepto de mi proyecto está basado puramente en la canción que escogí ya que tiene alto valor sentimental así que los elementos visuales que voy a planear estarán basados en el contenido de la letra, y los efectos y movimientos estarán más relacionados al ritmo de la canción. La canción que seleccioné es "RÍE COMO LO HICISTE AYER - Sa!koro" una canción ritmos suaves y tonos melancólicos.

Para los colores de mi proyecto quise escoger colores pálidos, ya que sus tonos son melancólicos.


<img width="508" height="361" alt="Captura de pantalla 2025-10-02 104946" src="https://github.com/user-attachments/assets/45308899-d49e-47ae-9966-33390cd7c186" />


Ya que decidí los colores que quiero para el proyecto debo empezar a pensar como usaré los campos de flujo o flocking, aunque creo que usaré los 2 para hacer un mejor efecto, me pondré a pensar un rato y volveré cuando tenga algo en mente.

Después de pensar un rato lo que quiero controlar yo en tiempo real es como se comportan las particulas, tengo pensado en implementar flowfield y flocking pero que sean diferentes modos que pueda alternar fácilmente, también modificar el flow field en tiempo real de manera que parezca que van al ritmo de las vocales. Esto va por buen camino, me gusta lo que estoy pensando.

Ya implementé las el flowfield con las partículas y se ve bastante genial

<img width="357" height="547" alt="image" src="https://github.com/user-attachments/assets/cbb06663-1328-45cd-ae6d-ba86653756fa" />


Ahora como quiero que no sea simplemente particulas moviendose por un campo de flujo, voy a analizar un poco la letra de la canción para ver que elementos visuales le puedo agregar a mi proyecto.

Analicé la letra y rescaté 3 elementos importantes que quiero implementar al proyecto, voy a poner las frases de dónde saqué estos elementos:

"Ver la última hoja caer y acostarla entre tus manos"
"Mira el cielo baby está nevando"

Quiero que al momento en dónde dice "Ver la última hoja caer" caigan unas hojas desde arriba del canva con su propio flowfield.

Y cuando dice "Mira el cielo baby está nevando" también quiero que empiece a nevar para darle más vida a esas visuales que estamos viendo, quizás hasta implemente una especie de copo de nieve que reaccione a la música sutilmente.

Voy a hacer unos pequeños bocetos para empezar a hacer una idea de como quiero que se vea el producto final.

<img width="2160" height="1620" alt="IMG_1427" src="https://github.com/user-attachments/assets/3372634a-2668-4dc0-8a07-12554e46f10d" />


---

Ha pasado mucho tiempo y tengo la cabeza hecha un bombo, han habido tantas correcciones con chatgpt, sin chatgpt que donde las ponga en esta bitácora, el archivo queda pesando 5GB, pero creo que finalmente conseguí lo que quería realizar, ojalá al profe le guste.

**CÓDIGO FUENTE**

```js

/*  Túnel performativo — Unidad 6 (NOC/Reynolds) [FPS + Calidad + Fix C/N + Snow Pool]
 *  - Glow configurable (GLOW_SCALE, GLOW_PD) — por defecto full-res + alta nitidez
 *  - Copo con halo real (compensa escala y aplica blur)
 *  - Marco pre-render + cache por paleta → sin lag al presionar C
 *  - Nieve con color más notorio + pooling y spawn suave → sin microtirones al activar N
 *  - FlowField typed arrays; agentes sin atan2; Panel/FFT throttling
 */

const P = {
  mode: 'tunnel',
  agentsCount: 320,
  agentSize: 2.6,
  glow: 14,
  blendAdd: true,

  fieldRes: 30,
  noiseInc: 0.08,
  swirl: 1.0,
  radial: 0.9,
  noiseWeight: 0.65,

  wAlign: 0.9, wCoh: 0.8, wSep: 1.25, neigh: 55,

  audioSensitivity: 0.9,
  audioReact: 0.85,

  snowOn: false,
  iceOn: false,
  frameOn: false,

  leafRainOn: false,
  leafFallSpeed: 1.0,

  // -- Ajustes nieve (visibilidad) --
  snowAlpha: 210,            // opacidad del disco de color
  snowGlowAlpha: 190,        // opacidad del halo en glow
  snowHighlightAlpha: 230,   // brillo blanco del centro
  snowColorBoost: 24         // +RGB para “levantar” el tono
};

const MAX_AGENTS = 1200;
const MAX_LEAVES = 450;

// --- Parámetros de glow del marco ---
const FRAME_GLOW_BLUR_MIN = 2.0;
const FRAME_GLOW_BLUR_MAX = 6.0;
const FRAME_GLOW_ALPHA    = 120;
const FRAME_GLOW_SCALE    = 1.6;

const Palette = {
  index: 0,
  list: [
    { name: 'Ice Blue',   base: [160,190,255], accent:[220,235,255] },
    { name: 'Pink Bloom', base: [235,150,235], accent:[255,195,245] },
    { name: 'Aurora Mint',base: [90,240,200],  accent:[160,255,235] },
    { name: 'Violet Arc', base: [150,130,255], accent:[210,190,255] },
    { name: 'Amber Glow', base: [255,185,90],  accent:[255,225,150] },
  ],
  current() { return this.list[this.index]; },
  next() { this.index = (this.index + 1) % this.list.length; }
};

// 🌬️ Viento global (nieve/hojas)
const Wind = {
  x: 0, targetX: 0,
  update() {
    this.targetX = map(noise(frameCount * 0.003), 0, 1, -0.6, 0.9);
    this.x = lerp(this.x, this.targetX, 0.02);
  }
};

let agents = [], flow, fft, track;
let snow = [], leaves = [];
let frameAlpha = 0, frameAlphaTarget = 0;

// === Calidad del glow ===
let glowG;
let GLOW_SCALE = 1.0;  // 1.0 = full-res
let GLOW_PD    = 2;    // 2 = más nítido (si pesa, bájalo a 1)
let lastAgentBlurPx = -1;

// Texturas cacheadas del marco (por paleta)
let frameHaloTex = null; // actual (en resolución de glowG)
let frameCoreTex = null; // actual (en resolución de pantalla)
let frameAlphaToDraw = 0;
let frameHaloByPalette = []; // [pIndex] => p5.Graphics
let frameCoreByPalette = []; // [pIndex] => p5.Graphics

// DOM
let playBtn, stopBtn, fileInput, urlInput, loadUrlBtn, srcLabel;
let cnv;

// Reloj
let lastLeafSpawn = 0;

// --- Nieve: pooling + spawn suave ---
const SNOW_POOL_MAX  = 700;  // tope duro de copos totales (activos + libres)
const SNOW_PREALLOC  = 350;  // cantidad a prealocar (evita tirón inicial)
let snowPool = [];           // pool libre de copos
let snowSpawnSmooth = 0;     // spawn target suavizado (EMA)
let snowSpawnAcc = 0;        // acumulador fraccional de spawn

// temp vecs
let tmpVec, tmpCenter;

// Throttling & cache
let lastPanelMs = 0;
let panelIntervalMs = 120;
let lastFFTMs = 0;
let cachedEnergy = { bass:0, mid:0, treble:0, level:0 };

// ---------- Utilidades ----------
function removeSwap(arr, idx){
  const last = arr.length - 1;
  if (idx < 0 || idx > last) return;
  const t = arr[last]; arr[last] = arr[idx]; arr[idx] = t;
  arr.pop();
}

function setGlowScale(s){
  GLOW_SCALE = constrain(s, 0.33, 1.0);
  makeGlowBuffer(width, height);
}
function setGlowPixelDensity(pd){
  GLOW_PD = constrain(pd|0, 1, 2);
  makeGlowBuffer(width, height);
}

// --- Pool helpers (nieve) ---
function prewarmSnowPool(){
  snowPool.length = 0;
  for (let i=0;i<SNOW_PREALLOC;i++) snowPool.push(new Snow(false));
}
function acquireSnow(){
  if (snowPool.length) return snowPool.pop();
  if (snow.length + snowPool.length < SNOW_POOL_MAX) return new Snow(false);
  return null;
}
function releaseSnowAt(idx){
  const s = snow[idx];
  removeSwap(snow, idx);
  snowPool.push(s);
}
function spawnSnowN(n){
  for (let i=0; i<n; i++){
    const s = acquireSnow();
    if (!s) break;
    s.reset(true);
    snow.push(s);
  }
}

// ---------- Setup ----------
function setup() {
  const sideW = (document.getElementById('sidebar')?.offsetWidth || 0);
  const w = Math.max(100, window.innerWidth - sideW);
  const h = window.innerHeight;

  cnv = createCanvas(w, h);
  cnv.parent('stage');
  pixelDensity(1);

  makeGlowBuffer(w, h);

  tmpVec    = createVector(0, 0);
  tmpCenter = createVector(0, 0);

  const scaleArea = (w * h) / (1280 * 720);
  P.agentsCount = floor(constrain(260 * Math.sqrt(scaleArea), 160, 650));

  playBtn    = document.getElementById('playBtn')    || null;
  stopBtn    = document.getElementById('stopBtn')    || null;
  fileInput  = document.getElementById('fileInput')  || null;
  urlInput   = document.getElementById('urlInput')   || null;
  loadUrlBtn = document.getElementById('loadUrlBtn') || null;
  srcLabel   = document.getElementById('srcLabel')   || null;

  if (playBtn)    playBtn.onclick    = togglePlay;
  if (stopBtn)    stopBtn.onclick    = () => { if (track) track.stop(); };
  if (fileInput)  fileInput.onchange = handleFileInput;
  if (loadUrlBtn) loadUrlBtn.onclick = handleUrlLoad;

  prepareAudio();

  agents.length = 0;
  for (let i = 0; i < P.agentsCount; i++) agents.push(new Agent(createVector(random(width), random(height))));
  flow = new FlowField(P.fieldRes);

  // Precalentar pool de nieve para evitar GC al activar con N
  prewarmSnowPool();

  background(10);
}

function windowResized() {
  const sideW = (document.getElementById('sidebar')?.offsetWidth || 0);
  const w = Math.max(100, window.innerWidth - sideW);
  const h = window.innerHeight;
  resizeCanvas(w, h);

  makeGlowBuffer(w, h);

  background(10);
  if (flow) flow.resize();
}

function makeGlowBuffer(w, h){
  if (glowG) glowG.remove();
  const gw = max(2, floor(w * GLOW_SCALE));
  const gh = max(2, floor(h * GLOW_SCALE));
  glowG = createGraphics(gw, gh);
  glowG.pixelDensity(GLOW_PD);
  glowG.clear();
  lastAgentBlurPx = -1;

  // reconstruir y cachear marcos para TODAS las paletas (una sola vez por resize/escala)
  buildAllFrameTextures();
  setFrameTexturesFromPalette();
}

// ---------- Audio ----------
function prepareAudio() {
  fft = new p5.FFT(0.9, 512);
  fft.setInput();
}
function handleFileInput(e) {
  const file = e.target?.files?.[0];
  if (!file) return;
  loadTrack(URL.createObjectURL(file), true);
}
function handleUrlLoad() {
  const url = (urlInput && urlInput.value || '').trim();
  if (!url) return;
  loadTrack(url, true);
}
function loadTrack(url, autoplay=false) {
  if (track) { try { track.stop(); } catch(_){} track = null; }
  userStartAudio();
  loadSound(url,
    snd => {
      track = snd;
      fft.setInput(track);
      track.setLoop(false);
      track.setVolume(0.9);
      if (autoplay) track.play();
      if (srcLabel) srcLabel.textContent = 'Fuente: track';
    },
    err => console.warn('No se pudo cargar la pista:', err)
  );
}
function togglePlay() {
  userStartAudio();
  if (track) track.isPlaying() ? track.pause() : track.play();
}

function audioEnergy() {
  const now = millis();
  if (now - lastFFTMs < 33) return cachedEnergy; // ~30Hz
  lastFFTMs = now;

  fft.analyze();
  const bass   = fft.getEnergy(20, 120)   / 255;
  const mid    = fft.getEnergy(250, 2000) / 255;
  const treble = fft.getEnergy(4000, 9000)/ 255;
  const level  = (bass*0.5 + mid*0.35 + treble*0.15);
  cachedEnergy = { bass, mid, treble, level };
  return cachedEnergy;
}
function clamp255(v){ return max(0, min(255, v|0)); }

// ---------- Draw ----------
function draw() {
  // trails
  noStroke(); fill(10,12,18,20); rect(0,0,width,height);

  Wind.update();
  if (P.mode !== 'flock' && flow) flow.update();

  const e = audioEnergy();
  const react = P.audioReact;
  const swirlNow  = P.swirl  * (1 + e.mid  * 0.35 * react);
  const radialNow = P.radial * (1 + e.bass * 0.45 * react);
  const glowNow   = P.glow   * (1 + e.bass * 0.60 * react) * P.audioSensitivity;
  const hueShift  = e.treble * 22 * react;

  // --- Capa de glow (procedural) ---
  glowG.clear();
  glowG.push();
  glowG.scale(GLOW_SCALE, GLOW_SCALE);

  // 1) Halos de agentes/nieve/hielo
  const agentBlurPx = max(0, glowNow) * GLOW_SCALE;
  if (abs(agentBlurPx - lastAgentBlurPx) > 0.25) {
    glowG.drawingContext.filter = `blur(${agentBlurPx.toFixed(2)}px)`;
    lastAgentBlurPx = agentBlurPx;
  }

  if (P.blendAdd) blendMode(ADD); else blendMode(BLEND);

  // --------- AGENTES ----------
  tmpCenter.set(width/2, height/2);
  const pal = Palette.current();
  const towardsK = (P.mode === 'tunnel') ? 0.035 : 0;

  for (let i=0;i<agents.length;i++) {
    const a = agents[i];

    if (P.mode === 'flock') {
      a.flock(agents, { wAlign: P.wAlign, wCoh: P.wCoh, wSep: P.wSep, neigh: P.neigh });
    } else {
      const F = flow.lookup(a.pos, { swirl: swirlNow, radial: radialNow });
      a.applyForce(tmpVec.set(F.x * 0.6, F.y * 0.6));
      if (towardsK !== 0) {
        let dx = tmpCenter.x - a.pos.x, dy = tmpCenter.y - a.pos.y;
        const mag = Math.hypot(dx, dy) || 1e-6;
        dx = dx / mag * towardsK; dy = dy / mag * towardsK;
        a.applyForce(tmpVec.set(dx, dy));
      }
    }

    a.update(); a.edges(); a.show(hueShift, pal, glowG);
  }

  // --------- NIEVE (pooling + spawn suave) ----------
  if (P.snowOn) {
    const target = map(e.bass, 0, 1, 0, 3);              // igual rango que antes
    snowSpawnSmooth = lerp(snowSpawnSmooth, target, 0.25); // EMA → evita picos al activar
    snowSpawnAcc += snowSpawnSmooth;
    const batch = snowSpawnAcc | 0; // floor
    if (batch > 0) {
      spawnSnowN(batch);
      snowSpawnAcc -= batch;
    }
  }
  // update/draw + release al pool
  for (let i=snow.length-1;i>=0;i--) {
    const s = snow[i];
    s.update(); s.draw(glowG);
    if (s.dead()) releaseSnowAt(i);
  }

  // --------- HOJAS ----------
  if (P.leafRainOn) spawnLeafRain();
  push(); blendMode(BLEND);
  for (let i=leaves.length-1;i>=0;i--) { const L=leaves[i]; L.update(); L.draw(); if (L.dead()) removeSwap(leaves,i); }
  pop();

  // --------- COPO (halo + base) ----------
  if (P.iceOn) drawIceOverlay(e, glowG);

  glowG.pop(); // fin procedural

  // --- Marco (HALO cacheado) ---
  frameAlpha = lerp(frameAlpha, frameAlphaTarget, 0.08);
  if (P.frameOn && frameAlpha > 0.01 && frameHaloTex) {
    glowG.drawingContext.filter = 'none';
    const prevA = glowG.drawingContext.globalAlpha;
    glowG.drawingContext.globalAlpha = (FRAME_GLOW_ALPHA * frameAlpha) / 255;
    // dibuja directamente en coords del buffer (sin scale)
    glowG.image(frameHaloTex, 0, 0);
    glowG.drawingContext.globalAlpha = prevA;
  }
  frameAlphaToDraw = (P.frameOn ? frameAlpha : 0);

  // --- COMPOSICIÓN DEL GLOW ---
  if (P.blendAdd) blendMode(ADD); else blendMode(BLEND);
  image(glowG, 0, 0, width, height);
  blendMode(BLEND);

  // --- Núcleo del marco (nítido) ---
  if (frameAlphaToDraw > 0.01 && frameCoreTex) {
    const prevA = drawingContext.globalAlpha;
    drawingContext.globalAlpha = frameAlphaToDraw;
    image(frameCoreTex, 0, 0, width, height);
    drawingContext.globalAlpha = prevA;
  }

  glowG.drawingContext.filter = 'none';
  drawPanelThrottled(e);
}

// ---------- Teclado ----------
function keyPressed() {
  if (key === 'F' || key === 'f') setMode('flock');
  else if (key === '4') setMode('flow');
  else if (key === '6') setMode('tunnel');

  else if (key === 'Q' || key === 'q') P.swirl = constrain(P.swirl - 0.05, 0, 3);
  else if (key === 'W' || key === 'w') P.swirl = constrain(P.swirl + 0.05, 0, 3);
  else if (key === 'A' || key === 'a') P.radial = constrain(P.radial - 0.05, -2, 2);
  else if (key === 'S' || key === 's') P.radial = constrain(P.radial + 0.05, -2, 2);

  else if (key === 'C' || key === 'c') { Palette.next(); setFrameTexturesFromPalette(); } // sin tirón

  else if (key === '[') P.leafFallSpeed = max(0.2, (P.leafFallSpeed - 0.1));
  else if (key === ']') P.leafFallSpeed = min(3.0, (P.leafFallSpeed + 0.1));

  else if (key === 'N' || key === 'n') {
    P.snowOn = !P.snowOn;
    if (P.snowOn) { // reset de acumuladores al encender → sin pico inicial
      snowSpawnSmooth = 0;
      snowSpawnAcc = 0;
      if (snowPool.length === 0) prewarmSnowPool();
    }
  }
  else if (key === 'I' || key === 'i') P.iceOn  = !P.iceOn;
  else if (key === 'D' || key === 'd') { P.frameOn = !P.frameOn; frameAlphaTarget = P.frameOn ? 1 : 0; }

  else if (key === 'h') spawnLeafTop();
  else if (key === 'J' || key === 'j') P.leafRainOn = !P.leafRainOn;

  else if (key === 'R' || key === 'r') regenerateFlowOnly();
  else if (key === 'U' || key === 'u') addAgentsSafely();

  // Ajuste rápido de resolución del glow
  else if (key === '+' || key === '=') setGlowScale(GLOW_SCALE + 0.1);
  else if (key === '-' || key === '_') setGlowScale(GLOW_SCALE - 0.1);
}
function setMode(m) { P.mode = (m === 'flock') ? 'flock' : (m === 'flow') ? 'flow' : 'tunnel'; }

// ---------- Agentes ----------
class Agent {
  constructor(pos) {
    this.pos = pos.copy();
    this.vel = p5.Vector.random2D().mult(random(0.5, 2));
    this.acc = createVector(0, 0);
    this.r = random(0.9, 1.4) * P.agentSize;
    this.maxSpeed = random(2.0, 3.0);
    this.maxForce = 0.08;
  }
  applyForce(f){ this.acc.add(f); }
  update(){
    this.vel.add(this.acc);
    const sp = this.vel.mag();
    if (sp > this.maxSpeed) this.vel.mult(this.maxSpeed / sp);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.r = lerp(this.r, P.agentSize, 0.08);
  }
  edges(){
    const b=12;
    if (this.pos.x < -b) this.pos.x = width + b;
    if (this.pos.y < -b) this.pos.y = height + b;
    if (this.pos.x > width + b) this.pos.x = -b;
    if (this.pos.y > height + b) this.pos.y = -b;
  }
  align(agents, neigh=55){
    const sum=createVector(); let count=0;
    for (let i=0;i<agents.length;i++){ const o=agents[i]; const d=p5.Vector.dist(this.pos,o.pos); if (o!==this && d<neigh){ sum.add(o.vel); count++; } }
    if (count>0){ sum.div(count).setMag(this.maxSpeed); return p5.Vector.sub(sum,this.vel).limit(this.maxForce); }
    return createVector(0,0);
  }
  cohesion(agents, neigh=55){
    const c=createVector(); let count=0;
    for (let i=0;i<agents.length;i++){ const o=agents[i]; const d=p5.Vector.dist(this.pos,o.pos); if (o!==this && d<neigh){ c.add(o.pos); count++; } }
    if (count>0){ c.div(count); return this.seekForce(c); }
    return createVector(0,0);
  }
  separation(agents, desired=22){
    const steer=createVector(); let count=0;
    for (let i=0;i<agents.length;i++){ const o=agents[i]; const d=p5.Vector.dist(this.pos,o.pos); if (o!==this && d<desired){
      const diff=p5.Vector.sub(this.pos,o.pos).normalize().div(d); steer.add(diff); count++; } }
    if (count>0) steer.div(count);
    if (steer.mag()>0){ steer.setMag(this.maxSpeed).sub(this.vel).limit(this.maxForce*1.4); }
    return steer;
  }
  flock(agents, cfg){
    this.applyForce(this.align(agents,cfg.neigh).mult(cfg.wAlign));
    this.applyForce(this.cohesion(agents,cfg.neigh).mult(cfg.wCoh));
    this.applyForce(this.separation(agents,22).mult(cfg.wSep));
  }
  seekForce(target){ const desired=p5.Vector.sub(target,this.pos).setMag(this.maxSpeed); return p5.Vector.sub(desired,this.vel).limit(this.maxForce); }

  show(hueShift=0,pal=Palette.current(), glowLayer=glowG){
    // color
    const spd = this.vel.mag();
    const t = (spd <= 0) ? 0 : constrain(spd / this.maxSpeed, 0, 1);
    let r=lerp(pal.base[0],pal.accent[0],t)+hueShift;
    let g=lerp(pal.base[1],pal.accent[1],t)+hueShift*0.4;
    let b=lerp(pal.base[2],pal.accent[2],t);
    r = clamp255(r); g = clamp255(g); b = clamp255(b);

    // dirección sin trig
    const inv = 1.0 / (spd || 1e-6);
    const ux = this.vel.x * inv, uy = this.vel.y * inv;
    const px = -uy, py = ux;

    const tip  = this.r*2.2;
    const tail = this.r*1.2;
    const half = this.r*0.9;

    const x1 = this.pos.x + ux*tip;
    const y1 = this.pos.y + uy*tip;
    const x2 = this.pos.x - ux*tail + px*half;
    const y2 = this.pos.y - uy*tail + py*half;
    const x3 = this.pos.x - ux*tail - px*half;
    const y3 = this.pos.y - uy*tail - py*half;

    // nítido
    noStroke(); fill(r,g,b,205);
    beginShape(); vertex(x1,y1); vertex(x2,y2); vertex(x3,y3); endShape(CLOSE);

    // halo
    glowLayer.noStroke(); glowLayer.fill(r,g,b, 200);
    glowLayer.beginShape(); glowLayer.vertex(x1,y1); glowLayer.vertex(x2,y2); glowLayer.vertex(x3,y3); glowLayer.endShape(CLOSE);
  }
}

// ---------- FlowField (typed arrays) ----------
class FlowField {
  constructor(res=30){
    this.res=res;
    this.zoff=random(1000);
    this.inc=P.noiseInc;
    this.resize();
    this._tmp = {x:0,y:0};
  }
  resize(){
    this.cols=floor(width/this.res)+1;
    this.rows=floor(height/this.res)+1;
    this.len = this.cols * this.rows;
    this.vx = new Float32Array(this.len);
    this.vy = new Float32Array(this.len);
    this.stamp = new Int32Array(this.len);
    for (let i=0;i<this.len;i++) this.stamp[i] = -1;
  }
  index(x,y){ return x + y * this.cols; }
  update(){ this.zoff+=0.005; }
  lookup(pos,opts={swirl:1,radial:1}){
    const xi=floor(constrain(pos.x/this.res,0,this.cols-1));
    const yi=floor(constrain(pos.y/this.res,0,this.rows-1));
    const idx=this.index(xi,yi);

    if (this.stamp[idx] !== frameCount){
      const angle = noise(xi*this.inc, yi*this.inc, this.zoff) * TWO_PI * 2;
      this.vx[idx] = Math.cos(angle);
      this.vy[idx] = Math.sin(angle);
      this.stamp[idx] = frameCount;
    }

    let vx = this.vx[idx] * P.noiseWeight;
    let vy = this.vy[idx] * P.noiseWeight;

    const cx = width*0.5, cy = height*0.5;
    let rx = cx - pos.x, ry = cy - pos.y;
    const mag = Math.hypot(rx, ry) || 1e-6;
    rx /= mag; ry /= mag;
    const tx = -ry, ty = rx;

    vx += rx * opts.radial + tx * opts.swirl;
    vy += ry * opts.radial + ty * opts.swirl;

    const m = Math.hypot(vx, vy);
    if (m > 1.6) { const s = 1.6 / m; vx *= s; vy *= s; }
    vx *= 0.9; vy *= 0.9;

    this._tmp.x = vx; this._tmp.y = vy;
    return this._tmp;
  }
}

// ---------- Motivos ----------
class Snow{
  constructor(init=false){
    if (init) this.reset(true);
    else {
      this.x=0; this.y=0; this.z=1;
      this.vy=1; this.driftAmp=0; this.a=0; this.r=1;
    }
  }
  reset(spawnFromTop=true){
    this.x = random(width);
    this.y = spawnFromTop ? -10 : random(-20, -6);
    this.z = random(0.2,1.2);
    this.vy = random(0.6,1.8)*this.z;
    this.driftAmp = random(0.12, 0.24);
    this.a = random(TWO_PI);
    this.r = random(0.8,2.2)*this.z;
  }
  update(){
    this.a += 0.02;
    this.x += Wind.x + sin(this.a) * this.driftAmp;
    this.y += this.vy;
  }
  draw(glowLayer){
    const pal=Palette.current();
    const cr = clamp255(pal.accent[0] + P.snowColorBoost);
    const cg = clamp255(pal.accent[1] + P.snowColorBoost);
    const cb = clamp255(pal.accent[2] + P.snowColorBoost);

    // halo en glow
    glowLayer.noStroke();
    glowLayer.fill(cr, cg, cb, P.snowGlowAlpha);
    glowLayer.ellipse(this.x,this.y,this.r*2,this.r*2);

    // disco de color nítido
    noStroke(); fill(cr, cg, cb, P.snowAlpha);
    ellipse(this.x,this.y,this.r*2,this.r*2);

    // highlight blanco en el centro
    fill(255, 255, 255, P.snowHighlightAlpha);
    ellipse(this.x, this.y, this.r*1.05, this.r*1.05);
  }
  dead(){ return this.y>height+8; }
}

// 🍂 Hojas
class Leaf{
  constructor(isRain=false){
    this.isRain = isRain;
    this.pos = createVector(random(width), -20);
    this.baseVy = isRain ? random(1.4, 2.2) : random(1.6, 2.6);
    this.vx = random(-0.05, 0.05);
    this.vy = this.baseVy;
    this.wobblePhase = random(TWO_PI);
    this.wobbleAmp = random(0.10, 0.22);

    this.rot = random(TWO_PI);
    this.rotSpeed = random(-0.015, 0.015);
    this.life = 0;
    this.maxLife = random(200, 300);
    this.scale = random(0.9, 1.25);
  }
  update(){
    this.life++;
    this.rot += this.rotSpeed;
    const targetVy = this.baseVy * P.leafFallSpeed;
    this.vy = lerp(this.vy, targetVy, 0.05);
    this.wobblePhase += 0.02;
    const lateral = Wind.x + sin(this.wobblePhase) * this.wobbleAmp;
    this.vx = lerp(this.vx, lateral, 0.1);
    this.pos.x += this.vx;
    this.pos.y += this.vy;
  }
  draw(){
    const pal=Palette.current();
    push();
    translate(this.pos.x,this.pos.y);
    rotate(this.rot);
    scale(this.scale);

    const fillR = clamp255(pal.base[0] + 10);
    const fillG = clamp255(pal.base[1] - 20);
    const fillB = clamp255(pal.base[2] - 10);
    const strokeR = clamp255(fillR - 25);
    const strokeG = clamp255(fillG - 25);
    const strokeB = clamp255(fillB - 25);

    stroke(strokeR, strokeG, strokeB, 200);
    strokeWeight(1);
    fill(fillR, fillG, fillB, 210);

    beginShape();
    vertex(0, -14);
    bezierVertex(16, -10, 16, 10, 0, 14);
    bezierVertex(-16, 10, -16, -10, 0, -14);
    endShape(CLOSE);

    stroke(strokeR, strokeG, strokeB, 220);
    line(0, -12, 0, 12);
    for (let j=1; j<=3; j++){
      const t = j/4, x = 0, y = lerp(-10, 10, t);
      const off = 6 + j*3.5;
      line(x, y, x + off, y - 3);
      line(x, y, x - off, y - 3);
    }
    pop();
  }
  dead(){ return this.life>this.maxLife || this.pos.x<-80 || this.pos.x>width+80 || this.pos.y>height+60; }
}

// ❄️ Copo (base nítida + halo compensado)
function drawIceOverlay(e, glowLayer){
  const pal=Palette.current();

  // === BASE NÍTIDA (igual al original) ===
  push();
  noFill();
  stroke(pal.accent[0], pal.accent[1], pal.accent[2], 140 + e.treble * 80);
  strokeWeight(1);

  const cx = width/2, cy = height/2;
  const branches = 12;
  const lenBase = min(width, height) * 0.45;
  for (let i = 0; i < branches; i++) {
    const ang = (TWO_PI / branches) * i + e.mid * 0.6;
    const len = lenBase * (0.6 + noise(i*0.2, frameCount*0.005) * 0.4);
    const x2 = cx + cos(ang) * len;
    const y2 = cy + sin(ang) * len;
    line(cx, cy, x2, y2);
    for (let j = 1; j < 4; j++) {
      const t = j/4;
      const px = lerp(cx, x2, t), py = lerp(cy, y2, t);
      const off = 10 + j * 6;
      line(px,py, px+cos(ang+HALF_PI)*off, py+sin(ang+HALF_PI)*off);
      line(px,py, px+cos(ang-HALF_PI)*off, py+sin(ang-HALF_PI)*off);
    }
  }
  pop();

  // === HALO EN glowLayer (compensa escala y activa blur) ===
  glowLayer.push();
  glowLayer.noFill();

  const sw = max(1.5 / (typeof GLOW_SCALE !== 'undefined' ? GLOW_SCALE : 1), 1.5);
  glowLayer.strokeWeight(sw);

  const react = P.audioReact || 0.85;
  const glowNow = (P.glow || 14) * (1 + e.bass * 0.60 * react) * (P.audioSensitivity || 0.9);
  const blurPx = max(0, glowNow) * (typeof GLOW_SCALE !== 'undefined' ? GLOW_SCALE : 1);
  glowLayer.drawingContext.filter = `blur(${blurPx.toFixed(2)}px)`;

  glowLayer.stroke(pal.accent[0], pal.accent[1], pal.accent[2], 180);

  const cxg = width/2, cyg = height/2;
  const branches2 = 12;
  const lenBase2 = min(width, height) * 0.45;
  for (let i = 0; i < branches2; i++) {
    const ang = (TWO_PI / branches2) * i + e.mid * 0.6;
    const len = lenBase2 * (0.6 + noise(i*0.2, frameCount*0.005) * 0.4);
    const x2 = cxg + cos(ang) * len;
    const y2 = cyg + sin(ang) * len;
    glowLayer.line(cxg, cyg, x2, y2);
  }
  glowLayer.pop();
}

// === Marco: construcción y cache por paleta ===
function buildAllFrameTextures(){
  frameHaloByPalette = new Array(Palette.list.length);
  frameCoreByPalette = new Array(Palette.list.length);
  for (let i=0;i<Palette.list.length;i++){
    const pair = buildFrameTexturesForPalette(Palette.list[i]);
    frameHaloByPalette[i] = pair.halo;
    frameCoreByPalette[i] = pair.core;
  }
}
function setFrameTexturesFromPalette(){
  frameHaloTex = frameHaloByPalette[Palette.index] || null;
  frameCoreTex = frameCoreByPalette[Palette.index] || null;
}
function buildFrameTexturesForPalette(pal){
  const r=pal.accent[0], g=pal.accent[1], b=pal.accent[2];
  const pad=12, step=22, dia=10;

  // HALO (buffer glow)
  const halo = createGraphics(glowG.width, glowG.height);
  halo.pixelDensity(GLOW_PD);
  halo.clear();
  halo.push();
  // Dibujamos en coords de pantalla → escalamos al buffer
  halo.scale(GLOW_SCALE, GLOW_SCALE);
  const bakedBlur = ((FRAME_GLOW_BLUR_MIN + FRAME_GLOW_BLUR_MAX) * 0.5) * GLOW_SCALE;
  halo.drawingContext.filter = `blur(${bakedBlur.toFixed(2)}px)`;
  halo.noStroke();
  halo.fill(r, g, b, 255);
  const haloD = dia * FRAME_GLOW_SCALE;
  for (let x=pad; x<width-pad; x+=step) {
    halo.ellipse(x, pad, haloD, haloD);
    halo.ellipse(x, height - pad, haloD, haloD);
  }
  for (let y=pad; y<height-pad; y+=step) {
    halo.ellipse(pad, y, haloD, haloD);
    halo.ellipse(width - pad, y, haloD, haloD);
  }
  halo.pop();

  // NÚCLEO (pantalla)
  const core = createGraphics(width, height);
  core.pixelDensity(1);
  core.clear();
  core.push();
  core.noStroke();
  // núcleo
  core.fill(r, g, b, 220);
  for (let x=pad; x<width-pad; x+=step) {
    core.ellipse(x, pad, dia, dia);
    core.ellipse(x, height - pad, dia, dia);
  }
  for (let y=pad; y<height-pad; y+=step) {
    core.ellipse(pad, y, dia, dia);
    core.ellipse(width - pad, y, dia, dia);
  }
  // highlights
  core.fill(255,255,255,180);
  for (let x=pad; x<width-pad; x+=step) {
    core.ellipse(x - dia*0.18, pad - dia*0.18, dia*0.35, dia*0.35);
    core.ellipse(x - dia*0.18, height - pad - dia*0.18, dia*0.35, dia*0.35);
  }
  for (let y=pad; y<height-pad; y+=step) {
    core.ellipse(pad - dia*0.18, y - dia*0.18, dia*0.35, dia*0.35);
    core.ellipse(width - pad - dia*0.18, y - dia*0.18, dia*0.35, dia*0.35);
  }
  core.pop();

  return { halo, core };
}

// ---------- Spawners / sistema ----------
function spawnLeafTop(){ if (leaves.length < MAX_LEAVES) leaves.push(new Leaf(false)); }
function spawnLeafRain(){
  const now = millis();
  const interval = 45; // ≈22 tandas/seg
  if (now - lastLeafSpawn < interval) return;
  lastLeafSpawn = now;

  const fr = frameRate();
  const batch = (fr > 58) ? 4 : (fr > 45) ? 3 : (fr > 30) ? 2 : 1;
  for (let i=0; i<batch; i++) {
    if (leaves.length >= MAX_LEAVES) break;
    leaves.push(new Leaf(true));
  }
}
function addAgentsSafely(){
  const fr = frameRate();
  let add = (fr > 58) ? 80 : (fr > 45) ? 50 : (fr > 30) ? 30 : 10;
  add = min(add, MAX_AGENTS - agents.length);
  for (let i=0; i<add; i++) agents.push(new Agent(createVector(random(width), random(height))));
}
function regenerateFlowOnly(){ flow = new FlowField(P.fieldRes); }

function drawPanelThrottled(e){
  const el = document.getElementById('panel'); if (!el) return;
  const now = millis();
  if (now - lastPanelMs < panelIntervalMs) return;
  lastPanelMs = now;

  const pal = Palette.current();
  el.innerHTML = `
    <strong>Params</strong><br>
    Modo: <b>${P.mode}</b> · Paleta: <b>${pal.name}</b><br>
    Agentes: ${agents.length} / ${MAX_AGENTS}<br>
    Swirl: ${P.swirl.toFixed(2)} · Radial: ${P.radial.toFixed(2)}<br>
    Nieve: ${P.snowOn?'ON':'OFF'} · Hielo: ${P.iceOn?'ON':'OFF'} · Marco: ${P.frameOn?'ON':'OFF'}<br>
    Lluvia hojas (J): ${P.leafRainOn?'ON':'OFF'} · Hojas activas: ${leaves.length}/${MAX_LEAVES}<br>
    Vel. hojas: ${P.leafFallSpeed.toFixed(2)}<br>
    FFT — Bass ${(e.bass*100)|0}% · Mid ${(e.mid*100)|0}% · Treble ${(e.treble*100)|0}%
  `;
}

```

**ENLACE AL SKETCH**

[Enlace al proyecto](https://editor.p5js.org/sebastr008/sketches/UghaEaagtC)

<img width="1311" height="772" alt="Captura de pantalla 2025-10-02 114641" src="https://github.com/user-attachments/assets/3a61a2c6-8473-45bc-8f71-9cba1336d0f9" />

<img width="1164" height="687" alt="Captura de pantalla 2025-10-02 114655" src="https://github.com/user-attachments/assets/a255cfcf-0b50-4614-a879-d2b9675d3667" />

## Calificación rúbrica

**Actividad 1**

Se evidencian capturas de pantalla de los trabajos de Tyler y se expresa que elementos le parecieron más llamativos

**Actividad 2** 

Se da respuesta a las 3 preguntas planteadas en la actividad

**Actividad 3**

Se explican las estructuras de datos usadas para el campo de flujo, también se describe como un agente usa este campo para calcular la fuerza de dirección, se listan los parametros claves y se evidencia la modificación al código con su explicación

**Actividad 4**

Se explican las 3 reglas del flocking, también se listan los parámetros clave utilizados y también se evidencia la modificación al código con su explicación

**Actividad 5**

Se evidencia todo el proceso de ideación y diseño con bocetos y decisiones, también se incluyo, el código fuente junto al enlace y algunas capturas de pantalla

**NOTA : 5.0**
