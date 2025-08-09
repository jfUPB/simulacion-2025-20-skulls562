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


### Actividad 5

Codigo (luego explico

```js
let t = 0;
let direccion = 1;

function setup() {
  createCanvas(680, 680);
}

function draw() {
  background(200);

  let v0 = createVector(50, 50);
  let v1 = createVector(500, 0);
  let v2 = createVector(0, 500);
  let v4 = createVector(-500, 500);
  let v5 = createVector(550, 50);

  let v3 = p5.Vector.lerp(v1, v2, t);
  let c1 = color('red');
  let c2 = color('blue');
  let c3 = lerpColor(c1, c2, t);

  drawArrow(v0, v1, 'red');
  drawArrow(v0, v2, 'blue');
  drawArrow(v0, v3, c3);
  drawArrow(v5, v4, 'purple');

  
  t += 0.01 * direccion;

  
  if (t >= 1) {
    t = 1;
    direccion = -1;
  } else if (t <= 0) {
    t = 0;
    direccion = 1;
  }
}

function drawArrow(base, vec, myColor) {
  push();
  stroke(myColor);
  strokeWeight(3);
  fill(myColor);
  translate(base.x, base.y);
  line(0, 0, vec.x, vec.y);
  rotate(vec.heading());
  let arrowSize = 7;
  translate(vec.mag() - arrowSize, 0);
  triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
  pop();
}
```

<img width="541" height="507" alt="image" src="https://github.com/user-attachments/assets/7d7119b2-b112-4ad1-914d-ee4e18e59293" />


### Actividad 6

Motion 101 es un marco básico de movimiento que usa tres vectores: posición, velocidad y aceleración. Geométricamente, cada frame se suman estos vectores en orden: aceleración a velocidad, velocidad a posición.

```js
let mover;

function setup() {
  createCanvas(600, 400);
  mover = new Mover();
}

function draw() {
  background(220);
  mover.update();
  mover.checkEdges();
  mover.show();
}

class Mover {
  constructor() {
    this.position = createVector(random(width), random(height));
    this.velocity = createVector(random(-2, 2), random(-2, 2));
  }

  update() {
    this.position.add(this.velocity);
  }

  show() {
    stroke(0);
    fill(175);
    circle(this.position.x, this.position.y, 48);
  }

  checkEdges() {
    if (this.position.x > width) {
      this.position.x = 0;
    } else if (this.position.x < 0) {
      this.position.x = width;
    }

    if (this.position.y > height) {
      this.position.y = 0;
    } else if (this.position.y < 0) {
      this.position.y = height;
    }
  }
}
```

### Actividad 7 

Aceleración constante:
El objeto se mueve cada vez más rápido en la misma dirección.
	•	Aceleración aleatoria:
El movimiento es caótico, como si el objeto estuviera siendo empujado al azar.
	•	Aceleración hacia el mouse:
El objeto parece “perseguir” el mouse, con un movimiento que se ajusta suavemente hacia su posición.
