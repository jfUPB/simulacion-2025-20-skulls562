# Evidencias de la unidad 6

# Actividad 1

Tyler Hobs

Imagen 1

<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/83fcc313-6bb6-4526-9dac-711757259c44" />

-¿Por qué me llama la atención?

Me gusta la dirección diagonal que crea una sensación de flujo continuo y me gustan las bandas de color que mantienen su identidad aunque se curvan.

-Lo que me inspira de esta imagen

Me inspira bastante el trabajar con streamlines largas que compartan el mismo camino.

Imagen 2

<img width="1920" height="1920" alt="image" src="https://github.com/user-attachments/assets/cf887cba-3e21-40ff-97d9-e474b83ff215" />

-¿Por qué me llama la atención?

Me gusta la textura tipo grafito/grabado que aparece con miles de trazos y tambien me gusta el contraste entre turbulencia local y dirección global.

-Lo que me inspira de esta imagen

Me inspira sumar curl noise para remolinos y bordes vivos y tambien me inspira usar trazos muy finos con baja opacidad para construir valor tonal.


# Actividad 2

Para mí, la steering force es una fuerza de guiado (de control) que uso para corregir mi velocidad actual y llevarla hacia una velocidad deseada. Matemáticamente la pienso así:

Deseado: un vector objetivo (depende del comportamiento).

Steer: steer = desired - velocity

Luego limito steer a maxforce y actualizo como aceleración (y la velocidad se limita con maxspeed).

Ejemplos rápidos de “deseado”:

Seek (ir a un objetivo): desired = normalize(target - pos) * maxspeed.

Arrive (frenar cerca): igual que seek pero reduciendo la magnitud del desired cuando estoy a poca distancia.

Flow field: desired = lookupCampo(pos) * maxspeed.

2) ¿En qué se diferencia de las fuerzas “físicas” que ya vimos?

Así lo entiendo y lo uso:

Intención: las fuerzas físicas (gravedad, rozamiento, viento, resortes, atracción) vienen del mundo; la steering viene de una decisión del agente (objetivo, regla o campo).

Cálculo: físicas = fórmulas basadas en posición/velocidad y constantes del entorno; steering = control “deseado − velocidad”, con clamps (maxforce, maxspeed) para estabilidad.

Teleología: físicas producen movimiento “emergente” pero no necesariamente orientado a metas; steering es goal-directed (seguir, evitar, alinearse, llegar).

Percepción local: en steering leo vecinos o un campo y mezclo varias fuerzas con pesos (ej., separación/alineación/cohesión). Las físicas no suelen combinarse con pesos “conductuales”.

Estabilidad/arte: al limitar maxforce/maxspeed, la steering me da curvas suaves y controlables; con solo fuerzas físicas es más difícil garantizar respuestas “creíbles” y suaves.

3) Relación con Craig Reynolds

Reynolds es la base de todo esto:

En “Boids” (1987) formaliza el movimiento colectivo con tres reglas locales (separación, alineación, cohesión). Cada regla genera una steering force; yo las sumo con pesos y limito.

En “Steering Behaviors for Autonomous Characters” (1999) cataloga comportamientos como seek, flee, arrive, wander, pursue/evade, path/flow following, obstacle avoidance, etc. Todos se implementan como variantes del mismo patrón: calcular un desired y convertirlo en steer limitado.

En resumen: steering es el marco de control que hace posible Boids y la animación conductual moderna; yo lo aplico igual en flow fields, flocking y navegación con obstáculos.



# Actividad 3

-Estructura del campo

Uso una cuadrícula con celdas de tamaño cellSize; guardo los vectores en un array 1D field[x + y*cols].

Cada celda almacena un p5.Vector unitario (dirección del flujo).

Genero el ángulo por noise (Perlin) y lo convierto con p5.Vector.fromAngle.


-Cómo sigo el campo (follow + steering)


Mapeo mi posición a la celda: gx=floor(x/cellSize), gy=floor(y/cellSize).

Tomo el vector de esa celda como deseado, lo pongo a maxspeed.

Calculo steer = desired - vel, lo limito a maxforce y lo aplico como aceleración.


-Parámetros clave


Resolución: cellSize.

Dinámica: maxspeed, maxforce.

Ruido: noiseScale (detalle) y noiseZInc (animación).


-Modificación que hice


Reemplacé Perlin por curl noise (vector tangente a las isolíneas del ruido).

Efecto observado: aparecen remolinos y rutas cerradas; los agentes forman bandas curvas más ricas. Con maxforce bajo, las curvas son suaves; con maxforce alto, reaccionan más brusco.



<img width="900" height="567" alt="image" src="https://github.com/user-attachments/assets/d202214f-4176-4bd8-adf9-c0f81175c704" />


Codgo modficado


```js

const dx = noise(xoff + eps, yoff, z) - noise(xoff - eps, yoff, z);
const dy = noise(xoff, yoff + eps, z) - noise(xoff, yoff - eps, z);
let v = createVector(dy, -dx).normalize();
this.field[this.index(x, y)] = v;

```

# Acividad 4

- Reglas

Separación: quiero evitar hacinamiento.
Cómo la calculo: miro vecinos muy cercanos; por cada uno sumo un vector desde el vecino hacia mí, normalizado e inverso a la distancia; promedio y lo convierto en steer (limito a maxforce).

Alineación: quiero moverme con la dirección promedio de mis vecinos.
Cómo la calculo: promedio sus velocidades; ese promedio lo llevo a maxspeed y hago desired - vel, limitando por maxforce.

Cohesión: quiero ir hacia el centro de masa del grupo.
Cómo la calculo: promedio sus posiciones; apunto del centro hacia mí con un seek (mismo patrón: desired - vel, limitado).


-Parámetros clave


Percepción: perceptionRadius (quién es vecino).

Pesos: cuánto pesa cada regla al combinarlas (ej. wSep, wAli, wCoh).

Límites dinámicos: maxspeed, maxforce.


-Modificación que hice


Cohesión = 0 (pongo wCoh = 0): el enjambre pierde tendencia a agruparse; veo bandas sueltas y dispersión, sobre todo si aumento wSep.

Percepción muy grande (ej. 120 px): los boids se alinean globalmente y tienden a formar grandes cardúmenes con menos detalle local.


<img width="894" height="554" alt="image" src="https://github.com/user-attachments/assets/8565297f-c186-4e3e-9b1a-fd19d39861fa" />


```js
W.coh = 0.0; // apago cohesión para observar dispersión
```


