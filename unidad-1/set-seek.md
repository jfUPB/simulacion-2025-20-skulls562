# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 1

la aleatoridad trae cosas muy bellas en el arte, que hace que esta se sienta viva y unica, dado a que de lo impredecible viene toda su belleza

### Actividad 2

1. el producto de sofi, tiene un gran aspecto aleatorio dado a que se basa en una cancion para tener su despliegue visual, no se rige por ningun movimiento en concreto, es natural y organico, tal como la pbra de aurora que ella muestra, esto genera patrones unicos que jamas en la vida seran repetidos, lo que le da mas emocion y sentido a la obtra

2. En mi vida profecional, me encantaria aplicar esto en mis produciones visuales, pudiendo unir mi musica y gustos con la parte visual viva, de poder trabajar en mis visuales mientras toco musica tal y como la banda que nos mostro el profe.


### Actividad 3

En estos momentos estamos analizando la funcion "walker" donde vemos como estye cambia respecto a lo que afectemos en la funcion step, en este caso lo siguiente
```js
  step() {
    const choice = floor(random(4));
    if (choice == 0) {
      this.x++;
    } else if (choice == 1) {
      this.x--;
    } else if (choice == 2) {
      this.y++;
    } else {
      this.y--;
    }
  }
}
```

aqui la funcion altera todo el movimiento del "walker", lo mas probable es que cuando cambie uno de los valores tambien altere toda la parte aleatora del movimiento, que pase de un movimiento aleatoreo pero seguido a uno mas erratico, mas parecido al caos, dado a que nuestro walker funciona asi

<img width="784" height="395" alt="image" src="https://github.com/user-attachments/assets/959a35fa-82d5-4d85-9655-a37363d60157" />

Tal y como analoice el valor altero todo el movimiento del "walker" lo altere a un floor mas grande y cambiando las elecciones que tenia y lo que paso fue que tuvo un movimientpo casi que vertical

```js
  step() {
    const choice = floor(random(7));
    if (choice == 0) {
      this.x++;
    } else if (choice == 2) {
      this.x--;
    } else if (choice == 4) {
      this.y++;
    } else {
      this.y--;
    }
```
y tuve una imagen asi 

<img width="324" height="150" alt="image" src="https://github.com/user-attachments/assets/e079eacb-fcb7-43b4-bded-4b729062d4d6" />

### Actividad 4

Estaba revisando el ejercio y lo principal es que este es de distrubuvion uniforme donde se puede predisponer la zona en concreto donde estamos dando mas la probabilidad de caer en cierto punto determinado a diferencia de la no univforme donde tiene a dispocision cualquier sentido y direccion

#### Uniforme
<img width="101" height="96" alt="image" src="https://github.com/user-attachments/assets/cfd49f57-d37f-4320-8010-a6d3d54555fe" />

#### No uniforme 
<img width="140" height="130" alt="image" src="https://github.com/user-attachments/assets/65ba0655-7ee7-4914-85c2-d14848e2cede" />

Alterando el codigo pude llegar a esta condicion de caos donde ya no delimito el movimiento de "y"

viendose asi 

<img width="104" height="104" alt="image" src="https://github.com/user-attachments/assets/5c26fee1-33c5-4d37-b60f-83491a63c8ca" />


### Actividad 5

con base al ejemplo pude desarrollar el siguiente codigo que me permite visualizar una forma diferente del Gaussian Distribution

```js
function setup() {
  createCanvas(640, 480);
  background(255);
}

function draw() {
  let x = randomGaussian(320, 60); 
  let y = 0; 

  noStroke();
  fill(0, 10);
  circle(x, y + frameCount % height, 16); 
}
```

lo que busco es hacer una linea mas grande que la que se nos muestra pero de igual forma delimitada y tambien quise hacerlo en vertical

<img width="643" height="478" alt="image" src="https://github.com/user-attachments/assets/ed17ee3d-2816-40c8-8cb1-cf732209c748" />

aqui el link https://editor.p5js.org/skulls562/sketches/c1imWwKBq


### Actividad 6

Basado en el ejemplo hice este para probar el movimiento tipo Lévy flight use una función que genera pasos pequeños pero a veces mete un salto grande, lo que hace que se vea más raro, la idea era ver una distribuciopn menos controlada que la normal aqui esta el codigfo

```js
let position;
let stepSize;

function setup() {
  createCanvas(640, 240);
  background(255);
  position = createVector(width / 2, height / 2);
}

function draw() {
  // Movimiento estilo Lévy flight
  let angle = random(TWO_PI);
  let r = levy(); // Usamos la función de Lévy
  let step = p5.Vector.fromAngle(angle).mult(r);

  position.add(step);

  // Limitar dentro del canvas
  position.x = constrain(position.x, 0, width);
  position.y = constrain(position.y, 0, height);

  noStroke();
  fill(0, 10);
  ellipse(position.x, position.y, 8, 8);
}

// Distribución estilo Lévy flight
function levy() {
  let beta = 1.5; // Exponente de la ley de potencia
  let num = pow(random(1), -1 / beta);
  return num;
}
```

y se ve tal que asi

<img width="591" height="247" alt="image" src="https://github.com/user-attachments/assets/7e8eec28-eb97-4253-9041-8672f42de4b0" />


y aqui esta el link https://editor.p5js.org/skulls562/sketches/mGeJKXOor


