# Unidad 3

## 🔎 Fase: Set + Seek

### Actividad 9

ejemplo 1: una pelota que se mueve y poco a poco se detiene porque el suelo genera fricción

visualmente la fricción hace que el objeto no se mueva eternamente, sino que “se apague” suavemente


```js
let pos, vel;

function setup() {
  createCanvas(400, 200);
  pos = createVector(50, height/2);
  vel = createVector(9, 0);
}

function draw() {
  background(220);
  
  let friction = vel.copy();
  friction.mult(-0.05);
  vel.add(friction);

  pos.add(vel);
  circle(pos.x, pos.y, 20);
}
```

<img width="395" height="196" alt="image" src="https://github.com/user-attachments/assets/3c4171d3-c706-48f8-bdd3-3e93f0f7959c" />



ejemplo 2: un círculo cae y atraviesa una zona de líquido . ahí la resistencia lo frena mucho más



```js
let pos, vel, acc;

function setup() {
  createCanvas(400, 300);
  pos = createVector(width/2, 0);
  vel = createVector(0, 0);
  acc = createVector(0, 0.2); // gravedad
}

function draw() {
  background(220);
  fill(180, 220, 255);
  rect(0, 150, width, 150); // zona de "agua"
  
  // gravedad
  vel.add(acc);
  
  // resistencia solo dentro del agua
  if (pos.y > 150) {
    let drag = vel.copy();
    drag.mult(-0.02 * vel.mag()); 
    vel.add(drag);
  }

  pos.add(vel);
  fill(200,0,0);
  circle(pos.x, pos.y, 20);
}
```
<img width="398" height="298" alt="image" src="https://github.com/user-attachments/assets/c6e6a296-89d7-453c-a796-1aa3f1d6b875" />




ejemplo 3:una partícula roja es atraída por un planeta azul en el centro.
visualiza cómo los cuerpos se atraen en el espacio

```js
let mover, planeta;

function setup() {
  createCanvas(400, 300);
  mover = createVector(100, 50);
  planeta = createVector(width/2, height/2);
  vel = createVector(1, 0);
}

function draw() {
  background(0);
  fill(0,0,255);
  circle(planeta.x, planeta.y, 40); 

  let force = p5.Vector.sub(planeta, mover);
  let d = constrain(force.mag(), 5, 25);
  let G = 1;
  let strength = G / (d * d);
  force.setMag(strength);
  vel.add(force);

  mover.add(vel);
  fill(255,0,0);
  circle(mover.x, mover.y, 10);
}
```

<img width="403" height="294" alt="image" src="https://github.com/user-attachments/assets/d1ad50f5-615b-45ed-a379-4b5eb095ef99" />


