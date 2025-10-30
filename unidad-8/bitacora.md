# Evidencias de la unidad 8

## Actividad 1 

Sónar+D CCCB 2020 — Carles Viarnès & Alba G. Corral (360º AV)

Le Parody & Alba G. Corral — Teatro Principal de Zaragoza (en directo)

Conexión sonido–imagen (observaciones): En Sónar+D sentí que la imagen no va “a metrónomo” sino a dinámica y timbre: trazos orgánicos que se estiran cuando la armonía abre y se densifican en crescendos. Los graves ensanchan la materia (más grosor y opacidad), los agudos añaden grano/brillo y puntuales destellos. Las transiciones visuales se comportan como fade-ins de parámetros (escala, dispersión, mezcla), no como cortes; eso sostiene la atmósfera.

En Le Parody hay más marcación rítmica: golpes percusivos disparan apariciones, estelas cortas o re-seeds del campo, y la voz empuja color/temperatura (cambia el “clima” más que la geometría). Se nota un diálogo entre capa base (paisaje vivo) y acentos (eventos que entran/salen sin romper el flujo).


## Actividad 2

Tema musical elegido: “Mal Comunicada” — Acrilla & Latin Mafia

Concepto visual (mi idea forza la conexión música–imagen): Quiero una doble corriente de energía que refleje la urgencia del DnB y la intensidad vocal: (1) un “loop infinito” que funciona como corriente continua —una memoria que no se cierra— y (2) una capa de partículas por detección de movimiento que interrumpe/interviene el flujo cuando yo actúo sobre la escena. Encima, una visualización de video con filtro de bordes en alfa: todo se cae a negro salvo los contornos, que quedan rojos como si el beat recortara siluetas.


Inputs interpretativos (por qué y para qué):

Audio (TouchDesigner / CHOPs): Audio Device In → Audio Spectrum/Analyze/Beat. Uso RMS para energía global (abre/cierra densidad del loop), FFT en 3 bandas (low/mid/high) para repartir “grosor / detalle / brillo”, y onsets/kicks para disparar eventos diferenciados en la capa de partículas. Los drums (snares/hi-hats) modulan dispersión y jitter.

Detección de movimiento (TOPs → CHOPs): Difference TOP → Blur → Threshold → Analyze TOP (área/centroide) → Math CHOP. Eso controla posición/rotación del loop infinito y la emisión de la segunda generación de partículas.

Control en vivo: teclas/MIDI/OSC para escenas (‘intro/buildup/drop/break’), ganancias de cada capa, re-seed del campo y exposición. Un Filter CHOP me da suavizado para que la pieza no parpadee.

Técnicas/algoritmos (en TouchDesigner, no p5.js):

Loop infinito: pipeline con Feedback TOP (Feedback → Transform → Level → Composite) para generar estelas controladas por audio; el kick sube opacidad y el snare altera escala/rotación.

Partículas por movimiento: Particle SOP o Instancing en Geo COMP (posición inicial desde puntos generados por la máscara de movimiento). La tasa de emisión depende del área detectada; ruido 3D para “wander”.

Bordes en rojo: Movie File In/Camera In → Edge TOP → Levels (alpha a bordes) → Multiply/Replace con rojo → mezcla en Composite TOP.

Enrutamiento: Math CHOP para mapear rangos, Limit/Clamp para seguridad, Lag/Filter para smoothing de performance.

Bocetos funcionales (cómo los inputs alteran el sistema):

Intro: loop fino, baja densidad; FFT-high abre microbrillo; bordes rojos muy tenues.

Buildup: snare rolls incrementan emisión y escala del loop; RMS eleva opacidad; comienzo a mover la capa con gestos.

Drop: kicks disparan bursts y un leve re-seed; bordes rojos recortan fuerte; los drums sacuden dispersión/jitter.

Break: cae la energía, sube difuminado en Feedback; preparo el siguiente estado.


## Actividad 3

mi sistema está implementado en TouchDesigner (no p5.js).


<img width="1296" height="893" alt="image" src="https://github.com/user-attachments/assets/6e358f92-606e-455e-938e-f069ddc21a11" />


## Rubrica

Completé las tres actividades (01, 02 y 03) con bitácora, código funcional, y ahora realizo esta autoevaluación. Por lo tanto, según la rúbrica, me califico con 5.
