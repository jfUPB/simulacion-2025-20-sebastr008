# Evidencias de la unidad 5

## Actividad 2

### Ejemplo 1:

**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

La creación de partículas empieza por la creación del array "particles" en dónde se almacenarán las partículas a mostrar. La clase "particle" tiene un constructor en dónde se le define la posicion de la particula, su aceleración, su velocidad y por último su "lifespan" también hay métodos dentro de esta clase como el run() que se encarga de llamar a los métodos "auxiliares" por así decirlo (métodos para aplicar fuerzas, mostrar la partícula y actualizar la velocidad y la aceleración). La desaparición de las partículas está hecha de manera que en cuanto el lifespan de cierta partícula llega a 0, se le hace un splice a esa particula, sacandola del array y dandole paso a la siguiente partícula para que se genere,

