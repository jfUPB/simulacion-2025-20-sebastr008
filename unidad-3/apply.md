# Unidad 3


## 🛠 Fase: Apply

### ¿Cómo modelé el problema de los n cuerpos?

#### Qué estoy simulando:

Cada “roca” es un cuerpo con:
- masa 
- posición 
- velocidad 
- aceleración 

En cada frame hago el ciclo de siempre: sumo fuerzas y luego actualizo "vel" y "pos".

Entre cada par de cuerpos calculo una atracción que depende de sus masas y de lo lejos que estén. Para que no explote cuando están pegados, uso un EPS (suavizado) en el denominador.

Básicamente esto quiere decir lejos = se atraen poquito, cerca = se atraen más, siempre uno hala al otro y al revés.


Código gravedad

```javascript
// --- n-cuerpos: gravedad newtoniana suavizada ---
const G = 0.45;    // fuerza base (ajustada "a ojo" para que se vea bien)
const EPS = 25;    // suavizado anti-singularidad

// fuerza entre dos cuerpos a y b
function applyGravity(a, b) {
  const dir = p5.Vector.sub(b.pos, a.pos);   // vector de a -> b
  const d2  = dir.magSq() + EPS;             // distancia^2 + suavizado
  const F   = (G * a.mass * b.mass) / d2;    // magnitud de la fuerza
  dir.normalize().mult(F);

  // acción y reacción: empujo a ambos en sentidos opuestos
  a.applyForce(dir);
  b.applyForce(p5.Vector.mult(dir, -1));
}

// se llama cada frame: recorre todos los pares i<j (sin repetirse ni tocarse a sí mismos)
for (let i = 0; i < plates.length; i++) {
  for (let j = i + 1; j < plates.length; j++) {
    applyGravity(plates[i], plates[j]);
  }
}
```


#### Cómo corre el frame (orden sencillo)
1. Pongo acc = 0 en cada cuerpo
2. Hago el doble bucle i<j y aplico las fuerzas de ambos lados
3. (En mi sketch también existen otras fuerzas como resortes/viento/drag, pero eso va aparte)
4. **Integro**: vel += acc, limito si hace falta (MAX_VEL), y luego pos += vel


#### Notas que me funcionaron
- EPS es clave: evita picos raros cuando dos cuerpos casi se chocan.
- Pongo topes de aceleración/velocidad en otro lado para que no se dañe todo.
- Siempre aplico acción y reacción (lo que empuja a uno, empuja al otro).

  [LINK AL PROYECTO](https://editor.p5js.org/sebastr008/sketches/DD689NNPH)

  <img width="922" height="780" alt="image" src="https://github.com/user-attachments/assets/78a069de-c825-4817-a479-42dc44ddf14c" />




