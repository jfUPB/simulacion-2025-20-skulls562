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

# Punto 5 Sistema con repulsor

Codigo original

```js
let emitter, repeller;

function setup(){
  createCanvas(640,360);
  emitter = new Emitter(width/2, 30);
  repeller = new Repeller(width/2, height-80, 40);
}

function draw(){
  background(255);
  emitter.addParticle();

  let gravity = createVector(0, 0.1);
  emitter.applyForce(gravity);

  for (let p of emitter.particles) {
    p.applyForce(repeller.repel(p));
  }

  emitter.run();
  repeller.show();
}

// (Emitter, Particle y Repeller como en el ejemplo base)
```

Original

<img width="376" height="323" alt="image" src="https://github.com/user-attachments/assets/5d80fe30-3aab-4700-93fa-82a4958a4d71" />

Modificaciones

arrastre cuadrático en una zona de fluido para disipar energía cuando la repulsión acelera demasiado

Optimizacion

Mismo patrón: lifespan + borrado backward en el emisor y el fluido no crea objetos extra por partícula, solo calcula una fuerza cuando está dentro de la región.

Imagen 

<img width="408" height="438" alt="image" src="https://github.com/user-attachments/assets/726c9397-e288-4b70-bc9d-a65d3c226ab3" />

Codigo

```js
let emitter, repeller, fluid;

function setup() {
  createCanvas(640, 360);
  emitter = new Emitter(width/2, 30);
  repeller = new Repeller(width/2, height-80, 50);
  fluid = new Fluid(0.0008, 0, height*0.45, width, height*0.55); // zona inferior
}

function draw() {
  background(248);

  emitter.addParticle();

  let gravity = createVector(0, 0.1);
  emitter.applyForce(gravity);

  for (let p of emitter.particles) {
    p.applyForce(repeller.repel(p));
    if (fluid.contains(p)) p.applyForce(fluid.drag(p)); // arrastre cuadrático
  }

  emitter.run();
  repeller.show();
  fluid.show();
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
      let p = this.particles[i];
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
    this.mass = 1;
  }
  applyForce(f){ this.acc.add(p5.Vector.div(f, this.mass)); }
  update(){
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show(){ noStroke(); fill(0, this.lifespan); circle(this.pos.x, this.pos.y, 6); }
  isDead(){ return this.lifespan <= 0; }
  run(){ this.update(); this.show(); }
}

class Repeller {
  constructor(x, y, r=40) {
    this.pos = createVector(x, y); this.r = r; this.strength = 100;
  }
  repel(p){
    let dir = p5.Vector.sub(p.pos, this.pos);
    let d = constrain(dir.mag(), 5, 200);
    dir.normalize();
    let force = this.strength / (d * d);
    return dir.mult(force);
  }
  show(){ noFill(); stroke(200, 40, 40); circle(this.pos.x, this.pos.y, this.r*2); }
}

class Fluid {
  constructor(k, x, y, w, h){ this.k=k; this.x=x; this.y=y; this.w=w; this.h=h; }
  contains(p){ return p.pos.x>this.x && p.pos.x<this.x+this.w && p.pos.y>this.y && p.pos.y<this.y+this.h; }
  drag(p){
    let speed = p.vel.mag();
    let dragMag = this.k * speed * speed;
    return p.vel.copy().mult(-1).setMag(dragMag);
  }
  show(){ noStroke(); fill(100,120,255,30); rect(this.x, this.y, this.w, this.h); }
}
```

Link: https://editor.p5js.org/skulls562/sketches/J3paYFA9x


## Actividad 3

Apply

# 1 Concepto

Un mapa vivo genera islas y un río que las conecta. Las partículas se comportan como olas y espuma; los barquitos navegan el flujo y dejan estelas. El usuario no pinta, compone corrientes y relieve para observar cómo emergen patrones.

Qué quiero comunicar: La memoria colectiva como flujo que conecta territorios; algunas huellas se depositan en la orilla, otras migran y se disipan.


# 2 Que uso de Herencia y polimorfismo

Clase base: Particle (pos, vel, acc, mass, lifespan, run(), applyForce()).
Subclases (polimórficas):
WaveCrest: cresta de ola dibujada como línea perpendicular a la velocidad; oscilación sutil y brillo cerca de costa.
WaveFoam: espuma de corta vida (usa el drag para disiparse rápido).


# 3 Que uso de otras unidades

U1 – Motion 101: vectores posición–velocidad–aceleración, vel.limit(), steering al campo de flujo.
U2 – Fuerzas: campo de flujo del río (−∇height), arrastre cuadrático en agua, atractor/repulsor local, fricción contextual en orillas.
U3 – Angular: orientación de barcos según rumbo; pequeñas rotaciones locales en partículas cerca de la costa.
U4 – Oscilaciones: seno para mecer crestas y espuma; modulación lateral de trayectoria.

# 4 Gestion de optimizacion

Por partícula: lifespan decreciente + eliminación backward con splice(i,1).
Por emisor: cupo máximo; cuando agota y queda sin partículas, se elimina.
Cap global: MAX_PARTICLES para mantener FPS; si se alcanza, pausa el spawn.
Disipación: drag cuadrático en agua acelera la “muerte útil” de las partículas.

# 5 interactividad

Click: emisor en agua (si haces click en tierra, busca agua cercana).
Shift+arrastrar: esculpir (Ctrl para bajar).
R: nuevo mapa. F: drag. A: atractor. P: asentamientos.
B: añadir barquito en agua.
↑/↓: spawn rate. +/-: nivel del mar. [ ]: brocha.
S: guardar captura.


# 6 Codigo

```js
// Archipiélago de Corrientes — Río + Islas + Barcos + Olas
// Ajustes pedidos:
// - Tierra en verde (más altura = más verde oscuro).
// - Barquitos que navegan el río, evitan islas, reaccionan al atractor y dejan estela.
// - Las partículas ahora son OLAS (crestas/espuma) que interactúan con costa/flujo.

// ---------- Parámetros del mapa ----------
let cellSize = 6;
let gridW, gridH;
let baseHeight, sculptOffset, gradX, gradY;
let seaLevel = 0.52;
let noiseScale = 0.008;
let islandScale = 1.0;
let noiseZ = 0;
// --- Esculpido del terreno ---
let brushRadius = 3;       // tamaño de la brocha (en celdas)  — ajusta con [ y ]
let brushStrength = 0.08;  // fuerza del trazo                   — Shift+arrastrar (Ctrl para bajar)


// ---------- Render ----------
let waterCol, landLoCol, landHiCol, shoreCol;

// ---------- Interacción / toggles ----------
let fluidOn = true;
let attractorOn = false;
let settlementsMode = false;

// ---------- Asentamientos (opcional, se conservan) ----------
let settlements = []; // {x,y,r,strength}

// ---------- Emisores y partículas (olas) ----------
let emitters = [];
let emissionRate = 4;
const MAX_PARTICLES = 1200; // subí un poco el cap para las olas
let ambientParticles = [];  // olas sueltas (estelas de barcos, etc.)

// ---------- Barcos ----------
let boats = [];

function setup() {
  createCanvas(960, 540);
  pixelDensity(1);

  gridW = floor(width / cellSize);
  gridH = floor(height / cellSize);

  baseHeight   = makeGrid(gridW, gridH, 0);
  sculptOffset = makeGrid(gridW, gridH, 0);
  gradX        = makeGrid(gridW, gridH, 0);
  gradY        = makeGrid(gridW, gridH, 0);

  // Agua azul
  waterCol = color(40, 120, 190);
  // Tierra en verde (claro -> oscuro con la altura)
  landLoCol = color(120, 170, 120); // verde claro
  landHiCol = color(22, 70, 42);    // verde oscuro
  shoreCol  = color(230, 245, 210); // línea de costa suave

  regenerateMap();

  // Emisor inicial en zona alta/mixta (ponlo donde quieras)
  emitters.push(new Emitter(width*0.5, height*0.25, 220));

  // Tres barquitos de arranque en zonas de agua
  for (let i = 0; i < 3; i++) {
    const p = randomWaterPoint(200);
    if (p) boats.push(new Boat(p.x, p.y));
  }
}

function draw() {
  background(12);

  // Actualizar gradiente (campo de flujo)
  updateGradients();

  // Dibujar mapa verde/azul
  drawTerrain();

  // Asentamientos (si los usas)
  drawSettlements();

  // ------- EMISORES / OLAS (con cap global) -------
  let total = totalParticles();
  for (let i = emitters.length - 1; i >= 0; i--) {
    const e = emitters[i];

    const canSpawn = total < MAX_PARTICLES;
    const n = canSpawn ? emissionRate : 0;
    e.spawn(n);
    total += e.justSpawned;

    // Fuerzas
    e.applyFlow();
    if (fluidOn) e.applyWaterDrag();
    if (attractorOn) e.applyAttractor(mouseX, mouseY, 160, 120);
    e.applySettlementAttractors();

    e.run();

    if (e.isFinished()) emitters.splice(i, 1);
  }

  // ------- BARQUITOS -------
  for (const b of boats) {
    b.applyFlow();
    if (attractorOn) b.applyAttractor(mouseX, mouseY, 180, 120);
    b.avoidLand();
    b.applyWaterDrag();
    b.run();
    // Wake (estela): pequeñas olas espuma
    if (frameCount % 2 === 0) {
      const tail = p5.Vector.mult(b.vel.copy().normalize(), -10);
      const pos = p5.Vector.add(b.pos, tail);
      if (isWater(pos.x, pos.y) && totalParticles() < MAX_PARTICLES) {
        ambientParticles.push(new WaveFoam(pos.x, pos.y, 0.65)); // espuma breve
      }
    }
  }

  // ------- OLAS SUELTAS (estelas, etc.) -------
  for (let i = ambientParticles.length - 1; i >= 0; i--) {
    const p = ambientParticles[i];
    // Igual que las demás: flujo + drag opcional
    const flow = sampleGradient(p.pos.x, p.pos.y);
    p.applyForce(flow);
    if (fluidOn) {
      const speed = p.vel.mag();
      const mag = p.dragK * speed * speed;
      const drag = p.vel.copy().mult(-1).setMag(mag);
      p.applyForce(drag);
    }
    p.run();
    if (p.isDead()) ambientParticles.splice(i, 1);
  }

  // HUD
  noStroke(); fill(235); textSize(12);
  text(
    `Emisores: ${emitters.length} | Olas: ${totalParticles()} | Barcos: ${boats.length} | Rate: ${emissionRate}/emisor | NivelMar: ${seaLevel.toFixed(2)} | Brocha: ${brushRadius}`,
    12, height - 12
  );
}

// ===============================
// INTERACCIÓN
// ===============================
function mousePressed() {
  if (settlementsMode) {
    if (isLand(mouseX, mouseY)) {
      settlements.push({ x: mouseX, y: mouseY, r: 70, strength: 120 });
    }
    return;
  }
  // Crear emisor (si está en tierra, lo reubicamos a agua cercana si se puede)
  const p = ensureWaterPosition(mouseX, mouseY, 80);
  if (p) emitters.push(new Emitter(p.x, p.y, 200));
}

function mouseDragged() {
  if (keyIsDown(SHIFT)) {
    const lower = keyIsDown(CONTROL);
    sculpt(mouseX, mouseY, brushRadius, brushStrength * (lower ? -1 : +1));
  }
}

function keyPressed() {
  if (key === 'r' || key === 'R') regenerateMap();
  if (key === 'f' || key === 'F') fluidOn = !fluidOn;
  if (key === 'a' || key === 'A') attractorOn = !attractorOn;
  if (key === 'p' || key === 'P') settlementsMode = !settlementsMode;
  if (key === 's' || key === 'S') saveCanvas('archipielago', 'png');

  if (keyCode === UP_ARROW) emissionRate = constrain(emissionRate + 1, 0, 25);
  if (keyCode === DOWN_ARROW) emissionRate = constrain(emissionRate - 1, 0, 25);

  if (key === '+') seaLevel = min(seaLevel + 0.01, 0.95);
  if (key === '-') seaLevel = max(seaLevel - 0.01, 0.05);
  if (key === '[') brushRadius = max(1, brushRadius - 1);
  if (key === ']') brushRadius += 1;

  // Añadir barquito
  if (key === 'b' || key === 'B') {
    if (isWater(mouseX, mouseY)) boats.push(new Boat(mouseX, mouseY));
    else {
      const p = ensureWaterPosition(mouseX, mouseY, 80);
      if (p) boats.push(new Boat(p.x, p.y));
    }
  }
}

// ===============================
// MAPA / TERRENO / FLUJO
// ===============================
function regenerateMap() {
  noiseZ = random(1000);
  for (let y = 0; y < gridH; y++) {
    for (let x = 0; x < gridW; x++) {
      const u = x * cellSize, v = y * cellSize;
      baseHeight[y][x] = terrainHeight(u, v, noiseZ);
      sculptOffset[y][x] = 0;
    }
  }
  updateGradients();
}

function terrainHeight(px, py, z) {
  const nx = px * noiseScale;
  const ny = py * noiseScale;
  let h = 0, amp = 1, freq = 1;
  for (let i = 0; i < 4; i++) {
    h += amp * noise(nx * freq, ny * freq, z);
    amp *= 0.5; freq *= 2.0;
  }
  h /= 1.875;
  h = pow(h, 1.2) * islandScale;
  return constrain(h, 0, 1);
}

function makeGrid(w, h, initVal=0) {
  const g = new Array(h);
  for (let j = 0; j < h; j++) {
    g[j] = new Array(w);
    for (let i = 0; i < w; i++) g[j][i] = initVal;
  }
  return g;
}

function updateGradients() {
  for (let y = 0; y < gridH; y++) {
    for (let x = 0; x < gridW; x++) {
      const hL = getHeightAtCell(max(0, x-1), y);
      const hR = getHeightAtCell(min(gridW-1, x+1), y);
      const hU = getHeightAtCell(x, max(0, y-1));
      const hD = getHeightAtCell(x, min(gridH-1, y+1));
      gradX[y][x] = (hR - hL) * 0.5;
      gradY[y][x] = (hD - hU) * 0.5;
    }
  }
}

function getHeightAtCell(cx, cy) {
  return baseHeight[cy][cx] + sculptOffset[cy][cx];
}

function sampleHeight(px, py) {
  const gx = constrain(px / cellSize, 0, gridW - 1.001);
  const gy = constrain(py / cellSize, 0, gridH - 1.001);
  const x0 = floor(gx), y0 = floor(gy);
  const x1 = x0 + 1,   y1 = y0 + 1;
  const tx = gx - x0,  ty = gy - y0;

  const h00 = getHeightAtCell(x0, y0);
  const h10 = getHeightAtCell(min(x1, gridW-1), y0);
  const h01 = getHeightAtCell(x0, min(y1, gridH-1));
  const h11 = getHeightAtCell(min(x1, gridW-1), min(y1, gridH-1));

  const hx0 = lerp(h00, h10, tx);
  const hx1 = lerp(h01, h11, tx);
  return lerp(hx0, hx1, ty);
}

function sampleGradient(px, py) {
  const gx = constrain(px / cellSize, 0, gridW - 1.001);
  const gy = constrain(py / cellSize, 0, gridH - 1.001);
  const x0 = floor(gx), y0 = floor(gy);
  const x1 = x0 + 1,   y1 = y0 + 1;
  const tx = gx - x0,  ty = gy - y0;

  function bilinear(grid) {
    const g00 = grid[y0][x0];
    const g10 = grid[y0][min(x1, gridW-1)];
    const g01 = grid[min(y1, gridH-1)][x0];
    const g11 = grid[min(y1, gridH-1)][min(x1, gridW-1)];
    const gx0 = lerp(g00, g10, tx);
    const gx1 = lerp(g01, g11, tx);
    return lerp(gx0, gx1, ty);
  }

  const gxv = bilinear(gradX);
  const gyv = bilinear(gradY);
  // Agua corre alto->bajo, usamos -grad como flujo base y amplificamos
  return createVector(-gxv, -gyv).mult(2.2);
}

function isWater(px, py) {
  return sampleHeight(px, py) < seaLevel;
}
function isLand(px, py) { return !isWater(px, py); }

function nearShore(px, py, eps = 0.02) {
  const h = sampleHeight(px, py);
  return abs(h - seaLevel) < eps;
}

function sculpt(px, py, radiusCells, strength) {
  const cx = floor(px / cellSize);
  const cy = floor(py / cellSize);
  for (let y = cy - radiusCells; y <= cy + radiusCells; y++) {
    if (y < 0 || y >= gridH) continue;
    for (let x = cx - radiusCells; x <= cx + radiusCells; x++) {
      if (x < 0 || x >= gridW) continue;
      const dx = x - cx;
      const dy = y - cy;
      const d = sqrt(dx*dx + dy*dy);
      if (d <= radiusCells) {
        const falloff = 1 - (d / (radiusCells + 0.0001));
        sculptOffset[y][x] = constrain(sculptOffset[y][x] + strength * falloff, -0.6, +0.6);
      }
    }
  }
}

function drawTerrain() {
  noStroke();
  for (let y = 0; y < gridH; y++) {
    const py = y * cellSize;
    for (let x = 0; x < gridW; x++) {
      const px = x * cellSize;
      const h = getHeightAtCell(x, y);
      if (h < seaLevel) {
        // agua: azul según profundidad
        const t = map(h, 0, seaLevel, 0, 1, true);
        const c = lerpColor(color(22, 70, 140), waterCol, t);
        fill(c);
      } else {
        // tierra: VERDE — más altura => más oscuro
        const t = map(h, seaLevel, 1, 0, 1, true);
        const c = lerpColor(landLoCol, landHiCol, t);
        fill(c);
      }
      rect(px, py, cellSize, cellSize);

      // línea de costa sutil
      if (abs(h - seaLevel) < 0.01) {
        fill(shoreCol);
        rect(px, py, cellSize, 1);
      }
    }
  }
}

function drawSettlements() {
  noFill();
  stroke(255, 230, 120, 90);
  for (const s of settlements) {
    circle(s.x, s.y, s.r*2);
    stroke(255, 230, 120, 200);
    point(s.x, s.y);
    stroke(255, 230, 120, 90);
  }
}

// ===============================
// UTILIDADES DE AGUA
// ===============================
function ensureWaterPosition(x, y, maxR = 60) {
  if (isWater(x, y)) return createVector(x, y);
  // busca agua cercana radialmente
  const steps = 24;
  for (let r = 6; r <= maxR; r += 6) {
    for (let a = 0; a < TWO_PI; a += TWO_PI / steps) {
      const px = x + cos(a) * r;
      const py = y + sin(a) * r;
      if (px >= 0 && px < width && py >= 0 && py < height && isWater(px, py)) {
        return createVector(px, py);
      }
    }
  }
  return null;
}

function randomWaterPoint(tries = 300) {
  for (let i = 0; i < tries; i++) {
    const x = random(width), y = random(height);
    if (isWater(x, y)) return createVector(x, y);
  }
  return null;
}

// ===============================
// EMISORES / OLAS
// ===============================
function totalParticles() {
  let t = ambientParticles.length;
  for (const e of emitters) t += e.particles.length;
  return t;
}

class Emitter {
  constructor(x, y, maxSpawn = 200) {
    this.origin = createVector(x, y);
    this.particles = [];
    this.maxSpawn = maxSpawn;
    this.spawned = 0;
    this.justSpawned = 0;
  }
  spawn(n) {
    this.justSpawned = 0;
    for (let i = 0; i < n; i++) {
      if (this.spawned >= this.maxSpawn) break;
      // Asegurar que la ola nace en agua
      const p = ensureWaterPosition(this.origin.x, this.origin.y, 50);
      if (!p) break;
      this.particles.push(createWaveParticle(p.x, p.y));
      this.spawned++; this.justSpawned++;
    }
  }
  applyFlow() {
    for (const p of this.particles) {
      const flow = sampleGradient(p.pos.x, p.pos.y);
      p.applyForce(flow);
    }
  }
  applyAttractor(cx, cy, radius=160, strength=120) {
    const center = createVector(cx, cy);
    for (const p of this.particles) {
      const dir = p5.Vector.sub(center, p.pos);
      const d = dir.mag();
      if (d < radius) {
        dir.normalize();
        const falloff = 1 - d / radius;
        p.applyForce(dir.mult(strength * falloff));
      }
    }
  }
  applyWaterDrag() {
    for (const p of this.particles) {
      const speed = p.vel.mag();
      const mag = p.dragK * speed * speed;
      const drag = p.vel.copy().mult(-1).setMag(mag);
      p.applyForce(drag);
    }
  }
  applySettlementAttractors() {
    if (settlements.length === 0) return;
    for (const p of this.particles) {
      for (const s of settlements) {
        const dir = createVector(s.x, s.y).sub(p.pos);
        const d = dir.mag();
        if (d < s.r) {
          dir.normalize();
          const f = s.strength * (1 - d / s.r);
          p.applyForce(dir.mult(f));
        }
      }
    }
  }
  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.run();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
  isFinished() { return this.spawned >= this.maxSpawn && this.particles.length === 0; }
}

// --------- Base "Partícula" (agente) ----------
class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.3, 1.2));
    this.acc = createVector();
    this.mass = random(0.8, 1.2);
    this.lifespan = 255;
    this.phase = random(TWO_PI);
    this.theta = random(TWO_PI);
    this.dragK = 0.0018; // drag cuadrático base en agua
  }
  applyForce(f) { this.acc.add(p5.Vector.div(f, this.mass)); }
  update() {
    this.vel.add(this.acc);
    this.vel.limit(3.0);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 1.8;
  }
  show() {}
  isDead() {
    return this.lifespan <= 0 ||
           this.pos.x < -80 || this.pos.x > width + 80 ||
           this.pos.y < -80 || this.pos.y > height + 80;
  }
  run(){ this.update(); this.show(); }
}

// --------- OLAS (herencia y polimorfismo) ---------
function createWaveParticle(x, y) {
  // 70% cresta, 30% espuma
  return (random() < 0.7) ? new WaveCrest(x, y) : new WaveFoam(x, y, 1.0);
}

// Cresta: línea perpendicular a la velocidad, con oscilación
class WaveCrest extends Particle {
  constructor(x, y) {
    super(x, y);
    this.dragK = 0.0016;
    this.ampl = random(2, 6);
    this.w = random(0.15, 0.22);
    this.col = color(220, 245, 255);
  }
  run() {
    // Oscilación lateral (U4)
    const fx = 0.05 * sin(frameCount * this.w + this.phase);
    this.applyForce(createVector(fx, 0));
    super.run();
  }
  show() {
    // normal (perpendicular a dirección)
    const v = this.vel.copy();
    const speed = max(0.001, v.mag());
    const n = createVector(-v.y, v.x).setMag(this.ampl * (1 + 0.7 * sin(frameCount * this.w + this.phase)));
    const p1 = p5.Vector.add(this.pos, n);
    const p2 = p5.Vector.sub(this.pos, n);

    // Más brillo cerca de costa
    const near = nearShore(this.pos.x, this.pos.y, 0.03);
    const a = near ? 255 : 180;

    stroke(red(this.col), green(this.col), blue(this.col), a);
    strokeWeight(1.6);
    line(p1.x, p1.y, p2.x, p2.y);
  }
}

// Espuma: burbuja rápida y corta vida (wake de barco, rompiente)
class WaveFoam extends Particle {
  constructor(x, y, lifeScale = 1.0) {
    super(x, y);
    this.dragK = 0.0025;
    this.lifespan = 160 * lifeScale;
    this.col = color(245, 250, 255);
    this.size = random(2, 4);
  }
  run() {
    // ligera oscilación y caída sutil hacia zonas de menor altura (más agua)
    const fx = 0.03 * sin(frameCount * 0.2 + this.phase);
    this.applyForce(createVector(fx, 0));
    super.run();
  }
  show() {
    noStroke();
    fill(red(this.col), green(this.col), blue(this.col), this.lifespan);
    circle(this.pos.x, this.pos.y, this.size);
  }
}

// ===============================
// BARCOS
// ===============================
class Boat {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(0.5);
    this.acc = createVector();
    this.mass = 3.0;
    this.maxSpeed = 2.2;
    this.dragK = 0.002; // drag cuadrático en agua
    this.hull = color(255, 210, 120);
  }
  applyForce(f) { this.acc.add(p5.Vector.div(f, this.mass)); }
  applyFlow() {
    const f = sampleGradient(this.pos.x, this.pos.y).mult(0.9); // ganancia menor que olas (barco más pesado)
    this.applyForce(f);
  }
  applyAttractor(cx, cy, radius=180, strength=120) {
    const center = createVector(cx, cy);
    const dir = p5.Vector.sub(center, this.pos);
    const d = dir.mag();
    if (d < radius) {
      dir.normalize();
      const falloff = 1 - d / radius;
      this.applyForce(dir.mult(strength * falloff));
    }
  }
  avoidLand() {
    // si está sobre tierra o muy pegado a la costa, empujar hacia agua
    const h = sampleHeight(this.pos.x, this.pos.y);
    if (h >= seaLevel - 0.005) {
      // usar -grad (bajada) para moverse a agua
      const flow = sampleGradient(this.pos.x, this.pos.y);
      // el flujo ya es -grad * gain; amplificar un poco
      this.applyForce(flow.mult(2.0));
      // leve repulsión perpendicular si la velocidad es casi cero
      if (this.vel.mag() < 0.3) {
        const jitter = p5.Vector.random2D().mult(0.6);
        this.applyForce(jitter);
      }
    }
  }
  applyWaterDrag() {
    const speed = this.vel.mag();
    const mag = this.dragK * speed * speed;
    const drag = this.vel.copy().mult(-1).setMag(mag);
    this.applyForce(drag);
  }
  update() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxSpeed);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }
  show() {
    push();
    translate(this.pos.x, this.pos.y);
    const ang = this.vel.heading();
    rotate(ang);
    noStroke();
    // sombra
    fill(0, 70);
    ellipse(2, 2, 14, 6);
    // casco
    fill(this.hull);
    beginShape();
    vertex(10, 0);
    vertex(-8, -4);
    vertex(-10, 0);
    vertex(-8, 4);
    endShape(CLOSE);
    // vela mínima (detalle)
    fill(255, 255, 255, 160);
    triangle(-2, -1, -6, -1, -6, -10);
    pop();
  }
  run() {
    this.update();
    this.show();
  }
}
```

# 7 captura 

<img width="949" height="472" alt="image" src="https://github.com/user-attachments/assets/ee9f7934-820b-4798-84e1-08bf192e7658" />
