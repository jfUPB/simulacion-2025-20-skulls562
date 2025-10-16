# Evidencias de la unidad 7

Empecé revisando varias piezas de Ji Lee y me concentré en cómo cada palabra “se programa” visualmente para decir lo que significa, sin necesidad de ilustración externa. Pensé cada ejemplo como un prototipo: qué partes “se transforman”, qué colisiona o se ancla, y qué mantendría estático si luego lo llevo a física.

“GRAVITY”.
La composición se desploma hacia abajo: las letras finales pierden alineación y caen ligeramente, como si el baseline estuviera roto. Eso comunica peso sin dibujo adicional. Si lo llevara a código, convertiría cada letra en un ‘body’ independiente y bajaría la rigidez de la “unión” al baseline; con mínima ‘restitution’ las letras “ceden” y quedan ladeadas.

<img width="479" height="481" alt="image" src="https://github.com/user-attachments/assets/8bb55946-0c06-4d21-8ebc-0db10e46515b" />



## Activida 2

Primero vi completo el video “p5.js Coding Tutorial | Introduction to matter.js” de Patt Vira y me paseé por los demos del sitio oficial de Matter.js. Me quedé con una idea clara: el “Engine” es el reloj, el “World” es la caja donde viven los cuerpos y las “Constraints” son la forma de articular piezas (bisagras, resortes). Con eso en mente monté dos experimentos mínimos para entender el flujo con p5.

Experimento Cajas y pelotas cayendo + arrastre con MouseConstraint

Qué hace: creo un mundo con gravedad, un suelo estático y paredes laterales. Genero cajas y círculos con propiedades físicas razonables. Puedo arrastrar cualquier cuerpo con el mouse y crear nuevos con click. Con R reinicio la escena.


<img width="859" height="576" alt="image" src="https://github.com/user-attachments/assets/6f72e069-fc33-4cd5-9562-0a558f2c9e6e" />


Link: https://editor.p5js.org/skulls562/sketches/ZjGJIXx8s



Me pasó que al principio el mouse no arrastraba nada; la razón era que estaba usando Mouse.create(window) en vez de Mouse.create(canvas.elt). Cambié eso y añadí mouse.pixelRatio = pixelDensity() y quedó preciso. Otra cosa: algunos cuerpos se “iban” por los bordes porque sólo tenía un suelo; resolví metiendo dos paredes estáticas afuera del canvas. Probé a escalar el canvas con CSS y la interacción se descalibraba; lo corregí dejando el canvas al tamaño real en createCanvas() y sin transformarlo por CSS. Finalmente, si Engine.update() no corría con un dt estable, la simulación se veía “tartamuda”; fijé 1000/60 y se estabilizó.




## Actividad 3

Elegí la palabra ROTAR. Arranco mostrando R _ I A R: falta la O y la I aún no es “t”. La O se comporta como rueda y rueda por el tope de las letras sobre un carril invisible. Cuando pasa por encima de la I, la barra superior aparece de forma progresiva hasta convertirla en t. Un embudo guía la rueda para que caiga limpia en su hueco y quede la palabra completa ROTAR.

Las letras las dibujo sólidas con p5. La física la hago con Matter: dos carriles estáticos inclinados muy sutil, un sensor centrado al palito de la I para disparar la animación, y un embudo con dos planos que aseguran la caída. El hueco de la O tiene topes laterales y piso para que asiente sin rebotar. La barra física de la “t” no aparece hasta que la rueda ya pasó, así no se traba.

En lo técnico: uso el motor y mundo de Matter actualizando en cada frame de p5. La O es un cuerpo circular con poca fricción y baja restitución para que ruede y no rebote raro. Mientras la O va por el riel le doy un empujoncito muy leve para evitar atascos. La barra de la “t” crece con un easing suave y está alineada ópticamente al palito (compensé ese desfasaje típico de la tipografía).

De interacción dejé un modo edición: puedo ver y mover/rotar los colisionadores (carriles, embudo, piso, etc.). Se activa con una tecla y permite arrastrar, rotar con shift o clic derecho, y hacer rotación fina con la rueda del mouse. Si algo queda chueco, lo ajusto ahí mismo. El reinicio ahora es solo manual.

También agregué sonido. Uno continuo de rodar (puedo elegir madera o metal) que se adapta a la velocidad de la rueda, y otro para el impacto cuando cae al piso del hueco (golpe seco o campana). Se activan al primer clic por el navegador y puedo mutear si quiero.

Pruebas y afinado: primero la “t” me quedaba corrida, así que la centré al palito con un pequeño offset. La O se me pegaba si la barra física estaba muy pronto, así que la creo después de que la rueda ya pasó. Para la caída, abrí el embudo y bajé fricciones; con eso entra derecha. El empuje constante en el riel me quitó microatascos.

Entrega: corrí todo en index.html y sketch.js (sin pegar código aquí). Tengo captura con la O encima de la I a mitad de transición y un GIF donde se ve todo el recorrido: R _ I A R → rueda por arriba → I se convierte en t → O cae y asienta. Controles básicos: edición, contornos, reinicio, tipos de sonido y mute.

Si hiciera otra iteración, convertiría la R inicial en colisionador compuesto para que la rueda “sienta” su hombro y probaría un modo cámara lenta para ver mejor el momento en que la I se vuelve t.


<img width="873" height="497" alt="image" src="https://github.com/user-attachments/assets/46ed8ef8-bd02-4105-b63d-f6abea642789" />


https://editor.p5js.org/skulls562/full/W9ZD8VoTG


## Autoevaluacion 

Completé las tres actividades (01, 02 y 03) con bitácora, código funcional, capturas y mejoras (edición de colisiones, sonido y alineación tipográfica), y ahora realizo esta autoevaluación. Por lo tanto, según la rúbrica, siento que mi nota puede ser 5.


