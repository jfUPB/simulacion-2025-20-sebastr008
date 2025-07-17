# Unidad 1

## 🔎 Fase: Set + Seek

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

