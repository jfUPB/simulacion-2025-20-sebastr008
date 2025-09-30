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







