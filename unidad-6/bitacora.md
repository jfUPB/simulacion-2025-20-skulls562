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




