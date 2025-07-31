# Unidad 2

## 🔎 Fase: Set + Seek

### Actividad 1

la suma de vectores en  p5.js funciona igual que la vida real, suma los componentes.

la funcion position = position + velocity no funciona dado a qie el signo "+" no esta sobrecargado, cuando esta intemntado sumar los vecores, cuando esto deberia de hacerse con la funcion


### Actividad 2

Tuve que convertir el código original que usaba variables x y y por separado para manejar la posición del caminante aleatorio, a una versión que utilizara un objeto p5.Vector. Esta conversión permite trabajar con posiciones de forma más eficiente y flexible, especialmente cuando se comienzan a aplicar operaciones vectoriales más complejas.

```js
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    // Creamos un vector para la posición
    this.position = createVector(width / 2, height / 2);
  }

  show() {
    stroke(0);
    point(this.position.x, this.position.y);
  }

  step() {
    const choice = floor(random(4));
    let step = createVector(0, 0);

    if (choice == 0) {
      step.x = 1;
    } else if (choice == 1) {
      step.x = -1;
    } else if (choice == 2) {
      step.y = 1;
    } else {
      step.y = -1;
    }

    // Sumar el paso aleatorio a la posición
    this.position.add(step);
  }
}
```


### Actividad 3

espero que primero se imprima [6, 9] y luego [20, 30], mostrando que el vector cambió.

```js
[6, 9]  
[20, 30]  
Only once
```

Al pasar un objeto (como un vector), los cambios afectan al original, los objetos en JavaScript se pasan por referencia, y se pueden modificar dentro de funciones pasan por referencia, lo que significa que cualquier cambio que se haga dentro de una función se reflejará fuera de ella. También vi cómo noLoop() hace que draw() solo se ejecute una vez, lo cual es útil para pruebas o visualizaciones estáticas


### Actividad 4 

1. mag() calcula la magnitud (longitud) de un vector.
magSq() da la magnitud al cuadrado, sin usar raíz cuadrada, por eso es más eficiente si no necesitas la longitud exacta

2. Convierte el vector a uno con la misma dirección pero magnitud 1

3.Sirve para saber qué tan alineados están dos vectores si el resultado es alto, apuntan en direcciones similares

4. 

Instancia: a.dot(b) usa el vector a

Estática: p5.Vector.dot(a, b) usa dos vectores sin depender de uno fijo

5.Da un vector perpendicular a los dos originales Su magnitud representa el área del paralelogramo que forman y su dirección depende de la orientación entre ellos 

6. Para saber la distancia entre dos puntos o vectores en el espacio.

7. 

normalize() mantiene dirección y ajusta magnitud a 1.

limit() impone un valor máximo a la magnitud, útil para limitar velocidad o fuerza.




