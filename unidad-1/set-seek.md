# Unidad 1

## 🔎 Fase: Set + Seek

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
