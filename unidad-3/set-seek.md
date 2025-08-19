# Unidad 3

## 🔎 Fase: Set + Seek


### 1. Resistencia de fluidos (Barco con estela)

#### Modelado de la fuerza


```js
let c = 0.2;
let v = barco.velocity.mag();
let dragMag = c * v * v;
let drag = barco.velocity.copy().mult(-1).normalize().mult(dragMag);
barco.applyForce(drag);
```

- `drag ∝ v^2` y en dirección opuesta al movimiento  
- Esto hace que el barco nunca se acelere para siempre

#### Relación con la obra
La resistencia que hay da la sensación de fluir en un medio y hace que el movimiento no sea caótico. El concepto que elegí fue ek de un barco que anda sin rumbo, dejando una estela como huella de su paso. Simbolizando como algunos andamos sin rumbo alguno, dejando nuestra huella por donde sea que pasamos

[LINK AL PROYECTO](https://editor.p5js.org/sebastr008/sketches/uCmJhjqvj)

<img width="788" height="380" alt="image" src="https://github.com/user-attachments/assets/0f4ff19b-2f7d-4f18-9142-f98b211fc222" />



### 2. Fricción (Dos esferas en superficies)

#### Modelado de la fuerza

```js
let friction = mover.velocity.copy();
friction.mult(-1);
friction.setMag(c); // c = coeficiente de fricción
mover.applyForce(friction);
```

- La magnitud depende de un coeficiente `c` que varía según la zona de la pantalla 
- Siempre actúa opuesta a la velocidad

#### Relación con la obra
La fricción muestra cómo el entorno tiene que ver con el movimiento, algunas superficies permiten avanzar más mientras que otras lo frenan
Lo pensé como una metáfora de caminar por terrenos distintos que cambian el esfuerzo necesario

[LINK AL PROYECTO](https://editor.p5js.org/sebastr008/sketches/rEpnnHRoj)

<img width="649" height="258" alt="image" src="https://github.com/user-attachments/assets/6d8529fd-29ec-42b9-ae24-69d5a6c0ae85" />




### 3. Atracción gravitacional (Universo infinito)

#### Modelado de la fuerza

```js
let dir = p5.Vector.sub(otro.pos, this.pos);
let d = constrain(dir.mag(), 5, 25);
let fuerza = (G * this.m * otro.m) / (d * d);
dir.normalize();
dir.mult(fuerza);
return dir;
```

- `F ∝ m1 * m2 / d²` (ley de gravitación universal)
- Se limita `d` para evitar valores infinitos

#### Relación con la obra
La atracción gravitacional crea una especie de "ballet cósmico".  
El concepto que quise trabajr es el infinito del universo: planetas que orbitan, chocan y explotan dejando restos que se vuelven nuevos planetas y genera un bucle infinito como el universo

[LINK AL PROYECTO](https://editor.p5js.org/sebastr008/sketches/P_oxlGkha)

<img width="887" height="565" alt="image" src="https://github.com/user-attachments/assets/bf1ec18b-3889-4bc8-a769-6dc5aaf3c3fe" />






