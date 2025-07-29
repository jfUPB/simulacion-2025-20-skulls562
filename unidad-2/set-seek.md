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

