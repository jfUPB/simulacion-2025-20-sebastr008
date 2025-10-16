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

## Actividad 3

### Palabra escogida : LOOP

### Idea conceptual:

Mientras hacía unas tareas que tenía pendientes para la clase de taller y como allí usamos Microsoft Loop para asignar nuestras tareas y objetivos, de una se me ocurrió la idea usar la palabra "Loop". Mi animación básicamente consiste en juntar las 2 "O" y volverlas un infinito, generando así un bucle sin fin por el que la bolita va a pasar. Adicionalmente quise que el fondo en donde se lleve a cabo esta animación sea en el universo, que también es una buena representación de algo infinito, como lo es un bucle.

### Aspectos técnicos clave

**Cómo hice las letras:**

No usé una fuente; dibujé caminos con matemáticas: la L, el ∞ y la p son curvas (Bézier cúbicas y una curva en forma de infinito hecha con senos y cosenos). Luego “partí” esas curvas en muchos puntitos (samples) que el lápiz debe seguir.

**La bolita) en Matter.js:**

La bolita es un Body circular. No la teletransporto: creo un target invisible que avanza por los puntitos a velocidad constante, y con Matter la bolita persigue ese target.

**Constraint (cuerda elástica invisible):**

Usé una Constraint que actúa como un resorte entre la bolita y el target (tiene stiffness y damping). Así la bolita llega suave y sin tirones, como si fuera con una liga.

**Propiedades físicas importantes:**

gravity = 0 (no queremos que se caiga).

frictionAir (rozamiento del aire) para que no oscile demasiado.

restitution bajita (para que no rebote bruscamente).

Le aplico una fuerza hacia el target (con un Vector normalizado) para que avance firme.

### Código 

**INDEX.HTML**

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />

  <!-- p5.js -->
  <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>

  <!-- Matter.js -->
  <script src="https://cdn.jsdelivr.net/npm/matter-js@0.20.0/build/matter.min.js"></script>

  <style>
    :root { --bg:#000000; --ui:#12161a; --text:#e7ecef; }
    html, body {
      height:100%; margin:0; background:var(--bg); color:var(--text);
      font:14px/1.35 system-ui,-apple-system,Segoe UI,Roboto,Inter,sans-serif;
    }
    .wrap { max-width:1080px; margin:18px auto; padding:12px 16px; }
    h1 { font-size:18px; margin:0 0 10px; font-weight:650; letter-spacing:.2px; }
    .toolbar {
      display:flex; gap:12px; align-items:center; flex-wrap:wrap;
      background:var(--ui); border-radius:14px; padding:10px;
      box-shadow:0 8px 28px rgba(0,0,0,.25);
    }
    .toolbar label { display:inline-flex; align-items:center; gap:8px; }
    .btn {
      background:#20242c; color:var(--text); border:1px solid #2a2f37;
      padding:8px 12px; border-radius:10px; cursor:pointer; user-select:none;
    }
    .btn:active { transform: translateY(1px) }
    #stage { margin-top:12px; }
    #stage canvas {
      display:block; width:100%; height:auto; background:transparent;
      border-radius:16px; box-shadow: inset 0 0 0 1px #1f232a, 0 16px 48px rgba(0,0,0,.35);
    }
    .hint { opacity:.85; margin-top:8px; }
    input[type="range"] { vertical-align:middle; }
  </style>
</head>
<body>
  <div class="wrap">

    <!-- Controles fuera del canvas -->
    <div class="toolbar">
      <button id="write" class="btn">Escribir (R)</button>
      <button id="reset" class="btn">Limpiar</button>
      <label>Velocidad <input id="speed" type="range" min="0.2" max="3.0" step="0.1" value="1.2"></label>
      <label>Grosor <input id="thickness" type="range" min="2" max="18" step="1" value="8"></label>
      <label>Inclinación <input id="slant" type="range" min="0" max="20" step="1" value="12"></label>
      <label>Espaciado <input id="tracking" type="range" min="0.70" max="1.15" step="0.01" value="0.85"></label>
    </div>

    <div id="stage"></div>
  </div>

  <script defer src="sketch.js"></script>
</body>
</html>

```


**SKETCH.JS**

```js
// p5.js + Matter.js
let W = 1000, H = 520;

// ====== Matter.js ======
let Engine, Bodies, Composite, Body, Constraint, Vector;
let engine, pen, target, spring;

// ====== UI ======
let speedEl, thickEl, slantEl, autoloopEl, trackEl;

// ====== Capas off-screen como p5.Graphics ======
let inkG, inkCtx;       // tinta persistente
let infG, infCtx;       // máscara ∞ para glow no acumulativo
let starG, starCtx;     // estrellas con estelas

// ====== Ruta / muestreo ======
const segs = [];
const defaultSamples = [];
const defaultTangents = [];
const STEPS_PER_SEG = 34;

const cumLenDefault = [];
let cumLenActive = null, totalLen = 0, progressLen = 0, lenIdx = 0;

let activeSamples = null, activeTangents = null;

// Métricas de escritura
let baseline, x0, asc, xHeight;

// Rango de ∞ para pintado especial + conexión a p
let infStart = 0, infEnd = 0;
let joinStart = 0, joinEnd = 0;

// Centrados y slant
let drawOffsetX = 0, drawOffsetY = 0;

// Fade in/out con espera
let progress = 0, lastDrawnIdx = 0, writing = false;
let fadeInT0 = 0, fadeOutT0 = 0, fadingOut = false, fadeOutScheduled = false;
const FADE_IN_MS = 700;
const FADE_OUT_MS = 800;
const HOLD_BEFORE_FADE_MS = 2500; // tu ajuste

// Partículas
const particles = [];
let bgG;

function drawGalaxyBackground(ctx){
  const g = ctx.createRadialGradient(W*0.55, H*0.45, 20, W*0.5, H*0.5, Math.max(W,H)*0.8);
  g.addColorStop(0, '#5E29E0');
  g.addColorStop(0.5, '#060814');
  g.addColorStop(1, '#7338B6');
  ctx.save();
  ctx.globalCompositeOperation = 'source-over';
  ctx.fillStyle = g;
  ctx.fillRect(0,0,W,H);
  ctx.restore();
}


// ====== Utiles Bezier ======
function cubicPoint(seg, t){
  const p0=seg.p0,c1=seg.c1,c2=seg.c2,p1=seg.p1;
  const u=1-t, tt=t*t, uu=u*u;
  return {
    x: uu*u*p0.x + 3*uu*t*c1.x + 3*u*tt*c2.x + tt*t*p1.x,
    y: uu*u*p0.y + 3*uu*t*c1.y + 3*u*tt*c2.y + tt*t*p1.y
  };
}
function cubicTangent(seg, t){
  const p0=seg.p0,c1=seg.c1,c2=seg.c2,p1=seg.p1;
  const u=1-t;
  return {
    x: 3*u*u*(c1.x-p0.x) + 6*u*t*(c2.x-c1.x) + 3*t*t*(p1.x-c2.x),
    y: 3*u*u*(c1.y-p0.y) + 6*u*t*(c2.y-c1.y) + 3*t*t*(p1.y-c2.y)
  };
}

function setup(){
  // p5 canvas
  const cnv = createCanvas(W, H);
  cnv.parent('stage');
  pixelDensity(window.devicePixelRatio || 1);

  // DOM
  speedEl    = document.getElementById('speed');
  thickEl    = document.getElementById('thickness');
  slantEl    = document.getElementById('slant');
  autoloopEl = document.getElementById('autoloop');
  trackEl    = document.getElementById('tracking');

  document.getElementById('write').addEventListener('click', startWriting);
  document.getElementById('reset').addEventListener('click', clearInk);
  window.addEventListener('keydown', (e)=>{ if((e.key||'').toLowerCase()==='r') startWriting(); });

  // Offscreen con p5.Graphics
  inkG  = createGraphics(W, H);  inkG.pixelDensity(pixelDensity());  inkCtx  = inkG.drawingContext;
  infG  = createGraphics(W, H);  infG.pixelDensity(pixelDensity());  infCtx  = infG.drawingContext;
  starG = createGraphics(W, H);  starG.pixelDensity(pixelDensity()); starCtx = starG.drawingContext;
  
  
  bgG = createGraphics(W, H);
  bgG.pixelDensity(pixelDensity()); // (opcional, para que se vea nítido en HiDPI)
  drawGalaxyBackground(bgG.drawingContext); // pintar la galaxia 1 vez


  // Matter.js
  Engine = Matter.Engine;
  Bodies = Matter.Bodies;
  Composite = Matter.Composite;
  Body = Matter.Body;
  Constraint = Matter.Constraint;
  Vector = Matter.Vector;

  engine = Engine.create();
  engine.gravity.y = 0;

  pen = Bodies.circle(120, H*0.55, 6, {
    density:0.002, frictionAir:0.18, restitution:0.2,
    render:{ fillStyle:'#00e5ff' }
  });
  Composite.add(engine.world, pen);

  target = Bodies.circle(pen.position.x, pen.position.y, 2, { isStatic:true, render:{ visible:false }});
  spring = Constraint.create({ bodyA: pen, bodyB: target, stiffness: 0.12, damping: 0.12, render:{ visible:false }});
  Composite.add(engine.world, [target, spring]);

  // Métricas
  baseline = H*0.64;
  x0 = 120;
  asc = baseline - 120;
  xHeight = baseline - 58;

  // Build & go
  buildDefaultPath();
  setActivePath();
  startWriting();

  // Listeners para rebuild
  trackEl.addEventListener('input', ()=>{ buildDefaultPath(); setActivePath(); startWriting(); });
  slantEl.addEventListener('input', ()=>{ recomputeOffsets(); startWriting(); });
}

let lastMs = performance.now();

function draw(){
  const now = performance.now();
  const rawDeltaMs = now - lastMs;
  const dt = Math.min(0.05, rawDeltaMs/1000);
  lastMs = now;

  image(bgG, 0, 0);            // ← galaxia base
  push(); blendMode(SCREEN);
  image(starG, 0, 0);          // ← estrellas con estela y glow (como ya lo tienes)
  pop();
// ...luego tinta, máscara ∞, partículas, bolita, etc.


  // Estrellas con estelas y glow
  updateStars(dt);
  push(); blendMode(SCREEN); image(starG, 0, 0); pop();

  // Avanzar escritura y target
  stepTarget(dt);
  const to = Vector.sub(target.position, pen.position);
  if ((to.x||0)!==0 || (to.y||0)!==0){
    const n = Vector.normalise(to);
    if (isFinite(n.x) && isFinite(n.y)) Body.applyForce(pen, pen.position, Vector.mult(n, 0.0004));
  }

  // FIX 2: clampo el delta de Matter a 16.667 ms
  Matter.Engine.update(engine, 1000/60);

  // Alpha del fade
  let alpha = 1;
  if(!fadingOut){
    const u = Math.min(1, (now - fadeInT0)/FADE_IN_MS);
    alpha = u*u*(3-2*u);
  } else {
    const u = Math.min(1, (now - fadeOutT0)/FADE_OUT_MS);
    alpha = 1 - (u*u*(3-2*u));
  }

  // Emitir partículas mientras escribe
  if (writing) emitParticles(pen.position.x, pen.position.y);

  // Tinta
  push(); drawingContext.globalAlpha = alpha; blendMode(BLEND); image(inkG, 0, 0); pop();

  // Glow del ∞ (no acumulativo)
  const pGlow = (Math.sin(now * 0.006) + 1) * 0.5;
  const glowAlpha = 0.55 + 0.35 * pGlow;
  const glowBlur  = 6 + 12 * pGlow;
  push();
  blendMode(SCREEN);
  drawingContext.globalAlpha = alpha * glowAlpha;
  drawingContext.filter = `blur(${glowBlur}px)`;
  image(infG, 0, 0);
  drawingContext.filter = 'none';
  pop();

  // Partículas
  updateParticles(dt);
  push(); drawingContext.globalAlpha = alpha; drawParticles(drawingContext); pop();

  // Bolita (cyan) con glow respirando
  push();
  const phase = (Math.sin(now * 0.006) + 1) * 0.5;
  const r = 5 + 1.5 * phase;
  drawingContext.shadowColor = 'rgba(0,229,255,0.85)';
  drawingContext.shadowBlur = 10 + 18 * phase;
  noStroke(); fill('#00e5ff'); circle(pen.position.x, pen.position.y, r*2);
  strokeWeight(1.5 + phase); stroke('rgba(0,229,255,0.9)'); noFill();
  circle(pen.position.x, pen.position.y, r*2);
  pop();
}

// ====== Construcción / escritura ======
function buildDefaultPath(){
  segs.length = 0; defaultSamples.length = 0; defaultTangents.length = 0;

  const baseUnit = 84;
  const t = (function(){ const v = parseFloat(trackEl && trackEl.value); return isNaN(v)?1:Math.max(0.6, Math.min(1.3, v)); })();
  const unit = baseUnit * t;

  let current = {x:x0-36*t, y: baseline+6};
  const C = (x,y)=>({x,y});
  const addCubic = (c1,c2,p1)=>{ segs.push({p0:{x:current.x,y:current.y}, c1, c2, p1}); current = {x:p1.x, y:p1.y}; };

  let prevSample = null;
  function pushSample(p, tan){
    if(prevSample && Math.hypot(p.x - prevSample.x, p.y - prevSample.y) < 0.3) return;
    defaultSamples.push(p);
    defaultTangents.push(tan?tan:(prevSample?{x:p.x-prevSample.x,y:p.y-prevSample.y}:{x:1,y:0}));
    prevSample = p;
  }
  function flushSegs(){
    let prev = null;
    for (let s=0; s<segs.length; s++){
      const seg = segs[s];
      for (let i=0;i<=STEPS_PER_SEG;i++){
        const tt = i/STEPS_PER_SEG;
        const p  = cubicPoint(seg, tt);
        const tan = cubicTangent(seg, tt);
        if(prev && Math.hypot(p.x - prev.x, p.y - prev.y) < 0.3) continue;
        pushSample(p, tan);
        prev = p;
      }
    }
    segs.length = 0;
  }

  // L (palo y barra)
  const stemTopY = asc - 6;
  const stemBaseY = baseline - 2;
  const stemX = x0 + 0.92*unit;

  current = { x: stemX, y: stemTopY };
  addCubic(C(stemX + 1, stemTopY + 18), C(stemX, stemBaseY - 12), { x: stemX, y: stemBaseY });
  const barEndX = stemX + 0.70*unit;
  addCubic(C(stemX + 24, stemBaseY + 0.2), C(barEndX - 18, stemBaseY + 0.2), { x: barEndX, y: stemBaseY });
  flushSegs();

  // ∞ (más redondo y grande)
  const postShift = 45;
  const infCx = x0 + 2.0*unit + postShift;
  const infCy = xHeight + 2;
  const infRx = 1.30*unit;
  const infRy = 1.05*unit;
  const loops = 2.5;

  function infPoint(theta){ return { x: infCx + infRx * Math.sin(theta), y: infCy + infRy * Math.sin(theta) * Math.cos(theta) }; }
  function infTangent(theta){ return { x: infRx * Math.cos(theta), y: infRy * Math.cos(2*theta) }; }

  const tStart = Math.PI;
  const pStart = infPoint(tStart);
  const tVec = infTangent(tStart);
  const tLen = Math.hypot(tVec.x, tVec.y) || 1;
  const tNorm = {x:tVec.x/tLen, y:tVec.y/tLen};

  // Conexión L → ∞
  const S = { x: barEndX, y: stemBaseY };
  const E = { x: pStart.x,  y: pStart.y  };
  const c1 = { x: S.x + 28, y: S.y - 6 };
  const c2 = { x: E.x - tNorm.x*42, y: E.y - tNorm.y*42 };
  addCubic(c1, c2, { x: E.x, y: E.y });
  flushSegs();

  // Trazado del ∞
  infStart = defaultSamples.length;
  const steps = Math.max(280, Math.floor(240*loops));
  for(let i=1;i<=steps;i++){
    const th = tStart + (i/steps) * Math.PI*2*loops;
    const p = infPoint(th);
    defaultSamples.push(p);
    defaultTangents.push(infTangent(th));
  }
  infEnd = defaultSamples.length - 1;

  // p (bowl + palito) — conexión ∞ → bowl (c1 EXACTO que pediste)
  const px = infCx + infRx + 0.42*unit;
  const rxB = Math.max(22, 30*t);
  const ryB = 26;
  const pBowlCx = px + rxB;
  const pBowlCy = baseline - 90;
  const pL_bowl = {x: pBowlCx - rxB, y: pBowlCy};

  joinStart = defaultSamples.length;
  const last = defaultSamples[ defaultSamples.length-1 ] || {x: infCx, y: infCy};
  addCubic(
    {x:last.x + 24*t, y:last.y - 33},    // c1 exacto
    {x:pL_bowl.x - 24*t,   y:pL_bowl.y - 10},
    {x:pL_bowl.x, y:pL_bowl.y}
  );
  flushSegs();
  joinEnd = defaultSamples.length - 1;

  // Bowl cerrado + palito corto
  const K = 0.5522847498;
  const pT_bowl = {x: pBowlCx,       y: pBowlCy - ryB};
  const pR_bowl = {x: pBowlCx + rxB, y: pBowlCy};
  const pB_bowl = {x: pBowlCx,       y: pBowlCy + ryB};

  addCubic({x: pL_bowl.x,         y: pL_bowl.y - K*ryB}, {x: pT_bowl.x - K*rxB, y: pT_bowl.y}, {x: pT_bowl.x,         y: pT_bowl.y});
  addCubic({x: pT_bowl.x + K*rxB, y: pT_bowl.y},         {x: pR_bowl.x,         y: pR_bowl.y - K*ryB}, {x: pR_bowl.x,   y: pR_bowl.y});
  addCubic({x: pR_bowl.x,         y: pR_bowl.y + K*ryB}, {x: pB_bowl.x + K*rxB, y: pB_bowl.y},         {x: pB_bowl.x,   y: pB_bowl.y});
  addCubic({x: pB_bowl.x - K*rxB, y: pB_bowl.y},         {x: pL_bowl.x,         y: pL_bowl.y + K*ryB}, {x: pL_bowl.x,   y: pL_bowl.y});

  const stemBottomY = baseline + 2;
  addCubic({x: px, y: pL_bowl.y + 8}, {x: px, y: stemBottomY - 8}, {x: px, y: stemBottomY});

  // FIX 1: muestrear bowl + palito (si no, la “p” no aparece)
  flushSegs();

  // Longitud acumulada (velocidad constante)
  cumLenDefault.length = 0;
  let L = 0;
  cumLenDefault.push(0);
  for (let i = 1; i < defaultSamples.length; i++){
    const a = defaultSamples[i-1], b = defaultSamples[i];
    if (isFinite(a.x) && isFinite(a.y) && isFinite(b.x) && isFinite(b.y)){
      L += Math.hypot(b.x - a.x, b.y - a.y);
    }
    cumLenDefault.push(L);
  }

  recomputeOffsets();
}

function setActivePath(){
  activeSamples  = defaultSamples;
  activeTangents = defaultTangents;
  cumLenActive = cumLenDefault;
  totalLen = cumLenActive.length ? cumLenActive[cumLenActive.length-1] : 0;
  lenIdx = 0;
  recomputeOffsets();
}

function clearInk(){
  inkG.clear();
  infG.clear();
  progress = 0; lastDrawnIdx = 0; progressLen = 0; lenIdx = 0;
}

function startWriting(){
  clearInk(); setActivePath();
  writing = true; progress = 0; progressLen = 0; lastDrawnIdx = 0; lenIdx = 0;
  fadingOut = false; fadeOutScheduled = false; fadeInT0 = performance.now();
  particles.length = 0;
}

function recomputeOffsets(){
  const k = Math.tan((parseFloat(slantEl.value)||0) * Math.PI/180);
  let minX=Infinity, minY=Infinity, maxX=-Infinity, maxY=-Infinity;
  for(const p of defaultSamples){
    if(!isFinite(p.x) || !isFinite(p.y)) continue;
    const qx = p.x + (p.y - baseline) * k;
    const qy = p.y;
    if(qx<minX) minX=qx; if(qy<minY) minY=qy; if(qx>maxX) maxX=qx; if(qy>maxY) maxY=qy;
  }
  const w = (maxX-minX)||1, h=(maxY-minY)||1;
  drawOffsetX = (W - w)/2 - minX;
  drawOffsetY = (H - h)/2 - minY;
}

function applySlant(p){
  const k = Math.tan((parseFloat(slantEl.value)||0) * Math.PI/180);
  return { x: p.x + (p.y - baseline) * k + drawOffsetX, y: p.y + drawOffsetY };
}

function isFiniteSample(p){ return !!p && isFinite(p.x) && isFinite(p.y); }

function drawSegment(a, b, w, style){
  const A = applySlant(a), B = applySlant(b);
  const isInf = style === 'inf';
  const isJoin = style === 'join';

  // Tinta en inkG
  const ctx = inkCtx;
  ctx.save();
  ctx.globalCompositeOperation = isJoin ? 'destination-over' : 'source-over';
  ctx.lineCap = 'round'; ctx.lineJoin = 'round';
  ctx.lineWidth = w;
  ctx.strokeStyle = isInf ? '#a7ff1a' : '#ffffff';
  ctx.beginPath(); ctx.moveTo(A.x, A.y); ctx.lineTo(B.x, B.y); ctx.stroke();

  if(!isInf && !isJoin){
    const g = ctx.createRadialGradient(B.x,B.y,w*0.2,B.x,B.y,w*1.0);
    g.addColorStop(0, 'rgba(255,255,255,0.25)');
    g.addColorStop(1, 'rgba(255,255,255,0)');
    ctx.fillStyle = g; ctx.beginPath(); ctx.arc(B.x,B.y,w*0.9,0,Math.PI*2); ctx.fill();
  }
  ctx.restore();

  // Máscara del ∞ (para glow no acumulativo) en infG
  if(isInf){
    const mctx = infCtx;
    mctx.save();
    mctx.lineCap = 'round'; mctx.lineJoin = 'round';
    mctx.strokeStyle = '#a7ff1a';
    mctx.lineWidth = w;
    mctx.beginPath(); mctx.moveTo(A.x, A.y); mctx.lineTo(B.x, B.y); mctx.stroke();
    mctx.restore();
  }
}

function stepTarget(dt){
  if(!writing || !activeSamples || activeSamples.length < 2) return;
  const s = parseFloat(speedEl.value) || 1;
  const v = 180 * s; // px/s
  progressLen = Math.min(totalLen, progressLen + dt * v);

  while (lenIdx < (cumLenActive.length - 1) && cumLenActive[lenIdx] < progressLen) lenIdx++;
  const maxIndex = lenIdx;

  const baseW = parseFloat(thickEl.value);
  for(let i=lastDrawnIdx+1; i<=maxIndex; i++){
    const a = activeSamples[i-1], b = activeSamples[i];
    if(!isFiniteSample(a) || !isFiniteSample(b)) continue;
    const t1 = activeTangents[Math.max(0,i-1)], t2 = activeTangents[i];
    const curv = (t1 && t2) ? Math.abs(Math.atan2(t2.y,t2.x) - Math.atan2(t1.y,t1.x)) : 0;
    const w = baseW * (0.9 + 0.5 * Math.min(1, curv*1.2));
    const style = (i >= infStart && i <= infEnd) ? 'inf' : ((i >= joinStart && i <= joinEnd) ? 'join' : 'norm');
    drawSegment(a, b, w, style);
  }
  lastDrawnIdx = maxIndex;

  // posicionar objetivo (target) en el último punto dibujado
  let k = maxIndex; while (k>0 && !isFiniteSample(activeSamples[k])) k--;
  Body.setPosition(target, applySlant(activeSamples[k]));

  // progreso total + programar fade-out diferido
  progress = totalLen > 0 ? (progressLen / totalLen) : 1;
  if(progress >= 1 && !fadingOut && !fadeOutScheduled){
    fadeOutScheduled = true;
    setTimeout(()=>{
      fadingOut = true; fadeOutT0 = performance.now();
      if(autoloopEl && autoloopEl.checked){
        setTimeout(startWriting, FADE_OUT_MS + 350);
      }
    }, HOLD_BEFORE_FADE_MS);
  }
}

// ====== Estrellas con estela y glow ======
const STAR_CT = 160;
const stars = new Array(STAR_CT).fill(0).map(()=>({
  x: Math.random()*W, y: Math.random()*H,
  vx: (Math.random()*2-1) * (8 + Math.random()*10),
  vy: (Math.random()*2-1) * (8 + Math.random()*10),
  r: 0.6 + Math.random()*1.8,
  s: 0.4 + Math.random()*1.4,
  a: Math.random()*Math.PI*2,
  burst: Math.random()<0.15 ? 1.0 : 0.0,
  hue: 200 + Math.random()*80,
  hueSpeed: (Math.random()*2-1) * 0.10
}));

function updateStars(dt){
  // Estelas: desvanecer MUY poco para que duren (tu 0.05)
  starCtx.save();
  starCtx.globalCompositeOperation = 'source-over';
  starCtx.fillStyle = 'rgba(0,0,0,0.12)';
  starCtx.fillRect(0,0,W,H);
  starCtx.restore();

  starCtx.save();
  starCtx.globalCompositeOperation = 'screen';
  for(const st of stars){
    st.x += st.vx * dt; st.y += st.vy * dt;
    if(st.x < -4) st.x += (W+8); else if(st.x > W+4) st.x -= (W+8);
    if(st.y < -4) st.y += (H+8); else if(st.y > H+4) st.y -= (H+8);

    // pulso + bursts
    st.a += 0.015*st.s; if (st.a > Math.PI*2) st.a -= Math.PI*2;
    let pulse = (Math.sin(st.a) + 1)*0.5;
    if(Math.random() < 0.002) st.burst = 1.0;
    if(st.burst > 0){ pulse = 0.5 + st.burst; st.burst *= 0.94; if(st.burst < 0.02) st.burst = 0; }

    // color HSL
    st.hue = (st.hue + st.hueSpeed * (dt*60)) % 360;
    const alpha = 0.55 + 0.40*pulse;
    const glow  = 6 + 24*pulse;
    const R = st.r * (0.8 + 1.8*pulse);

    starCtx.shadowBlur = glow;
    starCtx.shadowColor = `hsla(${st.hue}, 80%, 65%, ${0.55 + 0.35*pulse})`;
    starCtx.fillStyle   = `hsla(${st.hue}, 95%, 80%, ${alpha})`;
    starCtx.beginPath(); starCtx.arc(st.x, st.y, R*0.6, 0, Math.PI*2); starCtx.fill();
  }
  starCtx.restore();
}

// ====== Partículas de la bolita ======
function emitParticles(x,y){
  for(let i=0;i<2;i++){
    const ang = Math.random()*Math.PI*2;
    const sp = 30 + Math.random()*60;
    particles.push({
      x:x, y:y, vx: Math.cos(ang)*sp, vy: Math.sin(ang)*sp,
      life: 0, max: 0.6 + Math.random()*0.5, r: 1.2 + Math.random()*1.6
    });
  }
}
function updateParticles(dt){
  for(let i=particles.length-1;i>=0;i--){
    const p = particles[i];
    p.life += dt; if(p.life>p.max){ particles.splice(i,1); continue; }
    p.x += p.vx*dt; p.y += p.vy*dt; p.vx *= 0.96; p.vy *= 0.96;
  }
}
function drawParticles(ctx){
  for(const p of particles){
    const k = p.life/p.max;
    const a = 0.9*(1-k);
    const blur = 6 + 24*(1-k);
    ctx.shadowBlur = blur;
    ctx.shadowColor = 'rgba(0, 229, 255,' + (0.7*a) + ')';
    fill('rgba(120, 245, 255,' + a + ')'); noStroke();
    circle(p.x, p.y, p.r*(1+0.8*(1-k))*2);
  }
}

```

### Captura y GIF

![gif](https://github.com/user-attachments/assets/40c2beb4-b88a-4134-a4ca-c9e330c35e49)

<img width="995" height="517" alt="image" src="https://github.com/user-attachments/assets/77031cf6-411a-4c5f-af75-684cc091ba12" />


### Rúbrica 

Creo que merezco un 5.0 ya que:

Actividad #1: Se evidencian el análisis de más de 3 imágenes de la obra Word as Image del autor Ji Lee, también se evidencian 2 bocetos estáticos para representar las palabras "Loop" y "Adidas"

Actividad #2: Se evidencian los 2 códigos de experimentación usando Matter.js con sus respectivas capturas de pantalla, en dónde pruebo algunas propiedades de los cuerpos y diferentes maneras de generarlos y mostrarlos, adicionalmente se evidencia la explicación de los conceptos clave y también comento una dificultad que tuve.

Actividad #3: Se indica claramente la palabra escogida junto a su explicación conceptual, en dónde se muestra como la animación refuerza el sentido de la palabra. También se explica como fue la implementación de Matter al proyecto. Se evidencia también el código completo junto a un GIF y una captura de pantalla.


















