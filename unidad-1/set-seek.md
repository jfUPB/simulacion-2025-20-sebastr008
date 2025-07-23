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




