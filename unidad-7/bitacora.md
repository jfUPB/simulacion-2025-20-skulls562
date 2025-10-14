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
