# Evidencias de la unidad 5

## Actividad 1 

Al revisar la página de Refik Anadol me llamó la atención cómo utiliza grandes volúmenes de datos y los transforma en experiencias visuales inmersivas que parecen vivas. Sus obras muestran un uso avanzado de algoritmos generativos y sistemas de partículas que ni por el p*tas soy capaz de hacer todavia. Esto me conecta directamente con lo que estamos trabajando en la unidad, ya que a menor escala también buscamos crear composiciones a partir de reglas simples aplicadas en tiempo real. Ver su trabajo me inspira a pensar que lo que hacemos con p5.js es un primer paso hacia proyectos más complejos y ambiciosos en el campo del arte digital.


<img width="343" height="613" alt="image" src="https://github.com/user-attachments/assets/91f0087d-8d4a-4d1a-a706-281ab36b5038" />
(imagen de BIOME LUMINA #995/1000 el que mas me gusto)

## Actividad 2 

# Punto 1 Array de particulas 

Codigo original

```js
let particles = [];

function setup() {
  createCanvas(640, 240);
}

function draw() {
  background(255);
  particles.push(new Particle(width / 2, 20));
  //{!1} Loop through the array backward for deletion.
  for (let i = particles.length - 1; i >= 0; i--) {
    let particle = particles[i];
    particle.run();
    if (particle.isDead()) {
      particles.splice(i, 1);
    }
  }
}
```
Original
<img width="998" height="373" alt="image" src="https://github.com/user-attachments/assets/3dabfe9f-2bce-471e-825c-b28f9aaa8a04" />

Modificacion 

Aplico aceleración hacia el mouse, repasamos vectores, controlando magnitud para evitar explosiones de energía, mantiene el flujo estable sin tocar el esquema de memoria.


Optimizacion de memoria 

En cada draw() se empuja una partícula nueva al arreglo este se itera de atrás hacia adelante y si particle.isDead() es true, se elimina con splice(i,1), controlando el tamaño del arreglo

Imagen 

<img width="779" height="222" alt="image" src="https://github.com/user-attachments/assets/d887c0c5-eb55-4f37-be30-c56cf344bc4f" />

Codigo

```js
let particles = [];
const MAX_SPEED = 3;

function setup() {
  createCanvas(640, 360);
}

function draw() {
  background(250);
  particles.push(new Particle(width/2, 20));

  for (let i = particles.length - 1; i >= 0; i--) {
    let p = particles[i];
    p.seek(createVector(mouseX, mouseY));
    p.run();
    if (p.isDead()) particles.splice(i, 1);
  }
}

class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.5, 2));
    this.acc = createVector();
    this.lifespan = 255;
  }
  applyForce(f) { this.acc.add(f); }
  seek(target) {
    let desired = p5.Vector.sub(target, this.pos).setMag(0.2); // “fuerza” suave
    this.applyForce(desired);
  }
  update() {
    this.vel.add(this.acc);
    this.vel.limit(MAX_SPEED);   // Unidad 1: limitación de velocidad
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show() {
    noStroke();
    fill(30, this.lifespan);
    circle(this.pos.x, this.pos.y, 6);
  }
  isDead() { return this.lifespan <= 0; }
  run() { this.update(); this.show(); }
}
```

Link: https://editor.p5js.org/skulls562/sketches/qxX1XGcUH

# Punto 2 sistema de un sistema 

Codigo original

```js 
let emitters = [];

function setup() {
  createCanvas(640, 360);
}

function draw() {
  background(255);
  if (mouseIsPressed) {
    emitters.push(new Emitter(mouseX, mouseY));
  }
  for (let i = emitters.length - 1; i >= 0; i--) {
    emitters[i].addParticle();
    emitters[i].run();
    // (el ejemplo base normalmente no elimina emisores)
  }
}

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }
  addParticle() { this.particles.push(new Particle(this.origin.x, this.origin.y)); }
  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.run();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
}

class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D();
    this.acc = createVector();
    this.lifespan = 255;
  }
  applyForce(f) { this.acc.add(f); }
  update() {
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show() { noStroke(); fill(0, this.lifespan); circle(this.pos.x, this.pos.y, 6); }
  isDead() { return this.lifespan <= 0; }
  run(){ this.update(); this.show(); }
}
```

Original

<img width="880" height="328" alt="image" src="https://github.com/user-attachments/assets/5530b137-1907-41dc-9eeb-ea8121501c6f" />


Modificaciones

Añado fricción lineal F = -c·v en cada partícula para estabilizar además, limito la emisión por emisor y elimino el emisor cuando ya no tiene partículas ni cupos, esto es algo que vimos en la unidad 2

Optimizacion

Borrado backward de partículas con splice y creamos un cap de spawn por emisor y remoción del emisor vacío para evitar crecimiento indefinido del arreglo de sistemas.


Imagen 

<img width="747" height="417" alt="image" src="https://github.com/user-attachments/assets/03934498-858c-41ec-82da-8a185711cbfa" />

Codigo 

```js
let emitters = [];

function setup() {
  createCanvas(640, 360);
}

function draw() {
  background(250);

  if (mouseIsPressed) {
    emitters.push(new Emitter(mouseX, mouseY, 120)); // emite máx. 120
  }

  for (let i = emitters.length - 1; i >= 0; i--) {
    const e = emitters[i];
    e.addParticle();
    e.run();
    if (e.isFinished()) emitters.splice(i, 1);
  }
}

class Emitter {
  constructor(x, y, maxSpawn = 200) {
    this.origin = createVector(x, y);
    this.particles = [];
    this.spawned = 0;
    this.maxSpawn = maxSpawn;
  }
  addParticle() {
    if (this.spawned < this.maxSpawn) {
      this.particles.push(new Particle(this.origin.x, this.origin.y));
      this.spawned++;
    }
  }
  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.applyFriction(0.02); // Unidad 2
      p.run();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
  isFinished() {
    return this.spawned >= this.maxSpawn && this.particles.length === 0;
  }
}

class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(1, 3));
    this.acc = createVector();
    this.lifespan = 255;
  }
  applyForce(f) { this.acc.add(f); }
  applyFriction(c) {
    let friction = this.vel.copy().mult(-1).setMag(c);
    this.applyForce(friction);
  }
  update() {
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show(){ noStroke(); fill(0, this.lifespan); circle(this.pos.x, this.pos.y, 6); }
  isDead(){ return this.lifespan <= 0; }
  run(){ this.update(); this.show(); }
}
```

Link: https://editor.p5js.org/skulls562/sketches/sOevyEYez

# Punto 3 Herencia y polimorfismo

Codigo Original

```js
let emitter;

function setup() {
  createCanvas(640, 360);
  emitter = new Emitter(width/2, height/2);
}

function draw() {
  background(255);
  emitter.addParticle();   // agrega distintos tipos
  emitter.run();
}

class Emitter {
  constructor(x, y){ this.origin = createVector(x, y); this.particles=[]; }
  addParticle(){
    if (random() < 0.5) this.particles.push(new Particle(this.origin));
    else this.particles.push(new CrazyParticle(this.origin)); // subclase
  }
  run(){
    for (let i = this.particles.length-1; i>=0; i--){
      const p = this.particles[i];
      p.run();
      if (p.isDead()) this.particles.splice(i,1);
    }
  }
}

class Particle {
  constructor(o){ this.pos=o.copy(); this.vel=p5.Vector.random2D(); this.acc=createVector(); this.lifespan=255; }
  applyForce(f){ this.acc.add(f); }
  update(){ this.vel.add(this.acc); this.pos.add(this.vel); this.acc.mult(0); this.lifespan-=2; }
  show(){ noStroke(); fill(0,this.lifespan); circle(this.pos.x,this.pos.y,6); }
  isDead(){ return this.lifespan<=0; }
  run(){ this.update(); this.show(); }
}

class CrazyParticle extends Particle {
  show(){ super.show(); } // (estético diferente en el ejemplo completo)
}
```

Original

<img width="454" height="366" alt="image" src="https://github.com/user-attachments/assets/c7e540ef-2de4-4456-80ed-1da7687cbf0a" />

Modificacion

Aplicamos movimiento angular en dos subclases con rotación distinta SpinnerParticle con velocidad angular constante y TorqueParticle con aceleración angular usando ruido.

Optimizacion

Mantengo lifespan y borrado backward y polimorfismo solo afecta el dibujo, no la gestión de memoria.

Imagen 

<img width="455" height="420" alt="image" src="https://github.com/user-attachments/assets/6ef0bf71-23a3-4a3e-b1c6-5ab60b512523" />


Codigo

```js
let emitter;

function setup() {
  createCanvas(640, 360);
  emitter = new Emitter(width/2, height/2);
}

function draw() {
  background(250);
  emitter.addParticle();
  emitter.run();
}

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }
  addParticle() {
    if (random() < 0.5) this.particles.push(new SpinnerParticle(this.origin));
    else this.particles.push(new TorqueParticle(this.origin));
  }
  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.run();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
}

class Particle {
  constructor(origin) {
    this.pos = origin.copy();
    this.vel = p5.Vector.random2D().mult(random(0.5, 2));
    this.acc = createVector();
    this.lifespan = 255;
    this.theta = random(TWO_PI);
  }
  applyForce(f){ this.acc.add(f); }
  update(){
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  baseShow(){
    push(); translate(this.pos.x, this.pos.y); noStroke(); fill(30, this.lifespan);
    rectMode(CENTER); rect(0,0,10,4); pop();
  }
  isDead(){ return this.lifespan <= 0; }
  run(){ this.update(); this.show(); }
}

class SpinnerParticle extends Particle {
  constructor(o){ super(o); this.omega = random(-0.1, 0.1); }
  show(){
    push(); translate(this.pos.x, this.pos.y); rotate(this.theta);
    noStroke(); fill(10, this.lifespan); rectMode(CENTER); rect(0,0,12,4); pop();
    this.theta += this.omega;
  }
}

class TorqueParticle extends Particle {
  constructor(o){ super(o); this.omega = 0; this.alpha = 0; }
  show(){
    this.alpha = map(noise(frameCount*0.01 + this.pos.x), 0, 1, -0.02, 0.02);
    this.omega += this.alpha;
    this.theta += this.omega;
    push(); translate(this.pos.x, this.pos.y); rotate(this.theta);
    noStroke(); fill(0, this.lifespan); rectMode(CENTER); rect(0,0,16,3); pop();
  }
}
```

Link: https://editor.p5js.org/skulls562/sketches/vn4YObQ_P

# Punto 4 Sistema de particulas con fuezas

Codigo original

```js
let emitter;

function setup(){
  createCanvas(640,360);
  emitter = new Emitter(width/2, 40);
}

function draw(){
  background(255);
  let gravity = createVector(0, 0.1);
  emitter.applyForce(gravity);
  emitter.addParticle();
  emitter.run();
}

class Emitter {
  constructor(x,y){ this.origin=createVector(x,y); this.particles=[]; }
  addParticle(){ this.particles.push(new Particle(this.origin.x,this.origin.y)); }
  applyForce(f){ for (let p of this.particles) p.applyForce(f); }
  run(){
    for (let i=this.particles.length-1;i>=0;i--){
      let p=this.particles[i];
      p.run();
      if (p.isDead()) this.particles.splice(i,1);
    }
  }
}

class Particle {
  constructor(x,y){
    this.pos=createVector(x,y);
    this.vel=p5.Vector.random2D();
    this.acc=createVector();
    this.lifespan=255;
  }
  applyForce(f){ this.acc.add(f); }
  update(){ this.vel.add(this.acc); this.pos.add(this.vel); this.acc.mult(0); this.lifespan-=2; }
  show(){ noStroke(); fill(0,this.lifespan); circle(this.pos.x,this.pos.y,6); }
  isDead(){ return this.lifespan<=0; }
  run(){ this.update(); this.show(); }
}
```

Original

<img width="305" height="372" alt="image" src="https://github.com/user-attachments/assets/39f5fc3a-511a-4a8e-a898-40bbaa09f871" />


Modificacion

agrego fuerza sinusoidal lateral por partícula tratando con las ocilaciones de la unidad pasada

Optimizaciones

igual que el original: lifespan decrece y borrado backward y la fuerza oscilatoria no aumenta el conteo de objetos

Imagen 

<img width="383" height="458" alt="image" src="https://github.com/user-attachments/assets/00672aa9-4c71-476e-acc9-8db9df020a67" />


Codigo

```js
let emitter;

function setup() {
  createCanvas(640, 360);
  emitter = new Emitter(width/2, 40);
}

function draw() {
  background(250);

  let gravity = createVector(0, 0.12);
  emitter.applyForce(gravity);

  emitter.addParticle();
  emitter.run();
}

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }
  addParticle(){ this.particles.push(new Particle(this.origin.x, this.origin.y)); }
  applyForce(f){ for (let p of this.particles) p.applyForce(f); }
  run(){
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.applyOscillation();
      p.run();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
}

class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.5, 2));
    this.acc = createVector();
    this.lifespan = 255;
    this.phase = random(TWO_PI);
  }
  applyForce(f){ this.acc.add(f); }
  applyOscillation(){
    const A = 0.08;   // amplitud
    const w = 0.15;   // frecuencia
    const fx = A * sin(frameCount * w + this.phase);
    this.applyForce(createVector(fx, 0));
  }
  update(){
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show(){ noStroke(); fill(20, this.lifespan); circle(this.pos.x, this.pos.y, 6); }
  isDead(){ return this.lifespan <= 0; }
  run(){ this.update(); this.show(); }
}
```

Link: https://editor.p5js.org/skulls562/sketches/cITubjtuv
