# Unidad 1

## 🔎 Fase: Set + Seek


### Actividad 5

Modifiqué el ejemplo de la página para que las bolitas cubrieran solo la mitad de la pantalla, esto sigue siendo una distribución normal

```js
function setup() {
  createCanvas(400, 400);
}

function draw() {
 
  let x = randomGaussian(320, 60);
  let y = randomGaussian(60, 320);
  noStroke();
  fill(0, 10);
  circle(x, y, 16);
}
```
[Link Proyecto](https://editor.p5js.org/sebastr008/sketches/WB2BWf2ba)


<img width="391" height="409" alt="image" src="https://github.com/user-attachments/assets/e6ce7606-04c6-4807-a687-1419f5250e4c" />

### Actividad 6

En esta actividad le implementé el Levy Flight al Walker modificado que tiende ir hacia arriba y hacia la derecha, le quise implementar el levy flight para que surgieran trazos de diferentes partes del lienzo creando así una especie de lineas tambaleantes.

```js

let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  show() {
    stroke(0);
    point(this.x, this.y);
  }

  step() {
    let r = random(1);
    let choice = floor(random(100));
    
  if (r < 0.01) {
  this.x = random(0, 400);
  this.y = random(0, 100);
  } else if (choice >=0 && choice <=30) {


### Actividad 1

Una gran parte de la esencia del arte generativo está en su aleatoriedad ya que esta es la que permite que cada obra e incluso cada iteración del programa creado tenga su propia esencia y originalidad, sin esta, el arte generativo no sería tal cuál lo conocemos hoy, un caos hermoso y controlado.

### Actividad 2

La aletoriedad en la obra de Sofia se puede notar en los colores, la amplitud, la dirección y la velocidad de los trazos que varia según el ritmo de la canción, viéndose en las partes más animadas trazos más grandes, coloridos y veloces, mientras que en las partes más calmadas se notan trazos más delgados y lentos con cambios de dirección menos bruscos.

La aletoriedad según mi perfil profesional jugaría un papel bastante importante principalmente porque me quisiera dedicar a realizar proyectos parecidos a los que se hicieron en sis fisicos 1,2 y este curso, aparte, con el surgimiento de los llamados "visualistas", en dónde podemos crear visuales en tiempo real para cualquier evento en vivo, el componente principal de esto sería la aletoriedad para poder generar efectos e ideas nuevas.

### Actividad 3

Modificaré la función "step" de la clase "Walker" modificaré que si en la variable choice sale el número 2 o 4, la dirección (en x si es 2, y en y si es 4) no disminuirá si no que la multiplicaré por 2, haciendo así que lo que se dibuje en ningun momento vaya hacia la izquierda o hacia abajo.

```js
 step() {
    const choice = floor(random(4));
    if (choice == 0) {
      this.x++;
    } else if (choice == 1) {
      this.x*2;
    } else if (choice == 2) {
      this.y++;
    } else {
      this.y*2;
    }
  }
}
```

Le atine a que en ningún momento se vaya hacia la izquierda pero me confundí con el eje Y, dije que no se iría para abajo en ningún momento pero lo que realmente sucedió es que en ningún momento fue hacia arriba ajajaj

El profesor me aclaró la duda de por que se fue para abajo en vez de arriba, me explico que las Y positivas se cuentan hacia abajo.

### Actividad 4

La distribución uniforme es el resultado de que todas las probabilidades sean exactamente las mismas, mientras que la distribución no uniforme consiste en que las probabilidades estén más decantadas para un lado que para el resto de los demás,

Para que el código del walker vaya mas que todo a la derecha se podrían hacer aumentar el rango del random que se encuentra en el choice y poner un rango más amplio en donde la x se vaya a la derecha.

Algo así:

```js
  step() {
    let choice = floor(random(100));
    console.log(choice)
    if (choice >=0 && choice <=30) {
      this.x++;
    } else if (choice >=31 && choice <=40) {
      this.x--;
    } else if (choice > 41) {
      this.y--;
    } else {
      this.y++;
    }
  }
}
```

[Link al Proyecto](https://editor.p5js.org/sebastr008/sketches/qc9Cr9IkN)

<img width="443" height="171" alt="image" src="https://github.com/user-attachments/assets/bfb2ca2f-126f-401c-8d1f-575a81f8461b" />

### Actividad 7 

El ruido perlin genera valores aleatorios con la diferencia de que estos valores son muy cercanos unos con otros, haciendo que no haya cambios tan bruscos en los valores generados.

El concepto que quise lograr es como si estuvieras dentro de una nube y me basé en un ejemplo de la página para hacerlo.

```js
let zoff = 0; 

function setup() {
  createCanvas(640, 240);
}

function draw() {
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
      pixels[index + 3] = 255;    
      yoff += 0.01;
    }
    xoff += 0.01;
  }

  updatePixels();
  zoff += 0.01; 
}

```

[Link Proyecto](https://editor.p5js.org/sebastr008/sketches/vAcOBRRQU)

<img width="622" height="225" alt="image" src="https://github.com/user-attachments/assets/c226272a-a8a8-412a-afb2-c6a4fe8f821a" />

Entrar al proyecto para observar la animación.

