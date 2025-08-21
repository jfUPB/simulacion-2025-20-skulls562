# Unidad 3


## 🛠 Fase: Apply

### Actividad 10

Quise inspirarme en el problema de los n-cuerpos y en los móviles de Alexander Calder. Mi idea fue crear un sistema de pelotas que se atraen entre sí como planetas, pero que a veces forman “móviles” temporales, como si fueran pequeñas esculturas cinéticas en movimiento  Cuando dos pelotas se acercan mucho, aparecen barras y contrapesos que evocan a los móviles de Calder. Esto le da un aire escultórico a la simulación y hace que no sea solo física, sino también una obra artística en movimiento.

El usuario puede mover el mouse para crear un imán que atrae las pelotas, y con la rueda cambiar la fuerza de la gravedad. También incluí teclas para limpiar la pantalla o pausar, de modo que uno pueda “componer” diferentes escenas en tiempo real.

Que estoy usando 

Perlin noise: lo usé para generar un viento suave que cambia el movimiento de las pelotas y hace que los enlaces entre ellas se curven como si flotaran.

randomGaussian(): lo utilicé para darles velocidades iniciales más naturales, con variaciones que se sienten más orgánicas que un simple random uniforme.

Modelado del problema de n-cuerpos:
Cada pelota tiene una masa y se atrae con todas las demás usando la fórmula de la gravedad.

```js
// N-cuerpos — Móvil cósmico + referencia a Calder (móviles efímeros)
// Mouse: arrastra = imán | Rueda: G | N: viento por ruido | A: Calder ON/OFF | C: limpiar | P: pausa

let movers = [];
let sun;
let paused = false;
let useNoiseWind = true;
let artworks = []; // estallidos por alta velocidad
let mobiles = [];  // "móviles" a lo Calder (barras + contrapeso)
let calderMode = true;

// --- Parámetros ---
let NUM = 34;        // más pelotas
let G = 0.8;
let EPS = 20;
let DAMP = 0.999;
let TRAIL_FADE = 16;
let SPEED_ART_THRESHOLD = 5.2;
let BOUNCE = 0.9;

function setup() {
  createCanvas(800, 800);
  pixelDensity(1);
  background(0);
  noiseDetail(3, 0.5);
  colorMode(HSB, 360, 255, 255, 255);

  sun = new Mover(0, 0, 0, 0, 800, color(50, 240, 255)); // "amarillo"
  sun.isSun = true;

  for (let i = 0; i < NUM; i++) {
    let ang = random(TWO_PI);
    let r = random(120, 300);
    let pos = createVector(r * cos(ang), r * sin(ang));

    let vmag = abs(randomGaussian(2.2, 0.9)) + 0.4;
    let vel = pos.copy().rotate(HALF_PI);
    vel.setMag(vmag * random(0.9, 1.4));

    let m = random(6, 16);
    let col = calderPalette(i);
    movers.push(new Mover(pos.x, pos.y, vel.x, vel.y, m, col));
  }
}

function draw() {
  if (paused) return;

  // Estela
  noStroke();
  fill(0, TRAIL_FADE);
  rect(0, 0, width, height);

  translate(width / 2, height / 2);

  // --- Fuerzas y enlaces móviles ---
  for (let i = 0; i < movers.length; i++) {
    let a = movers[i];

    sun.attract(a);

    for (let j = i + 1; j < movers.length; j++) {
      let b = movers[j];
      a.attract(b);
      b.attract(a);

      let d = p5.Vector.dist(a.pos, b.pos);

      // Hilos fluidos
      if (d < 220) {
        let alpha = map(d, 0, 220, 160, 0);
        stroke(hue(a.col), 180, 255, alpha);
        strokeWeight(0.6);
        noFill();
        let mid = p5.Vector.lerp(a.pos, b.pos, 0.5);
        let n = noise(mid.x * 0.006, mid.y * 0.006, frameCount * 0.006);
        let curve = p5.Vector.fromAngle(n * TWO_PI).mult(18);
        bezier(a.pos.x, a.pos.y,
               mid.x + curve.x, mid.y + curve.y,
               mid.x - curve.x, mid.y - curve.y,
               b.pos.x, b.pos.y);
      }

      // --- Referencia Calder: crear móvil efímero al acercarse ---
      if (calderMode && d > 40 && d < 140 && random() < 0.02) {
        maybeCreateMobile(a, b);
      }
    }

    // Viento por ruido
    if (useNoiseWind) {
      let t = frameCount * 0.004;
      let k = 0.0025;
      let ang = noise(a.pos.x * k, a.pos.y * k, t) * TWO_PI * 2.0;
      let wind = p5.Vector.fromAngle(ang).mult(0.02 + 0.04 * noise(a.pos.y * k, t));
      a.applyForce(wind);
    }
  }

  // Imán con mouse
  if (mouseIsPressed) {
    let mpos = screenToWorld(mouseX, mouseY);
    for (let a of movers) {
      let f = p5.Vector.sub(mpos, a.pos);
      let d2 = max(EPS, f.magSq());
      f.setMag(1400 / d2);
      a.applyForce(f);
    }
  }

  // Integración + rebote + arte por velocidad
  sun.update();
  sun.constrainToBounds();
  //sun.show();

  for (let a of movers) {
    a.update();
    a.constrainToBounds();
    a.triggerArtIfFast();
    a.show();
  }

  // Dibujar móviles (Calder)
  renderMobiles();

  // Dibujar estallidos
  renderArtworks();

  // HUD
  resetMatrix();
  drawHUD();
}

// ---------- Clases y utilidades ----------
class Mover {
  constructor(x, y, vx, vy, m, col = color(200, 200, 255)) {
    this.pos = createVector(x, y);
    this.vel = createVector(vx, vy);
    this.acc = createVector(0, 0);
    this.m = m;
    this.col = col;
    this.sz = sqrt(m) * 2.2;
    this.cooldown = 0;
    this.isSun = false;
  }
  applyForce(f) {
    let a = p5.Vector.div(f, this.m);
    this.acc.add(a);
  }
  attract(other) {
    let dir = p5.Vector.sub(this.pos, other.pos);
    let d2 = dir.magSq() + EPS;
    dir.normalize();
    let strength = (G * this.m * other.m) / d2;
    dir.mult(strength);
    other.applyForce(dir);
  }
  update() {
    this.vel.add(this.acc);
    this.vel.mult(DAMP);
    this.pos.add(this.vel);
    this.acc.mult(0);
    if (this.cooldown > 0) this.cooldown--;
  }
  constrainToBounds() {
    let minX = -width / 2 + this.sz * 0.5;
    let maxX =  width / 2 - this.sz * 0.5;
    let minY = -height / 2 + this.sz * 0.5;
    let maxY =  height / 2 - this.sz * 0.5;
    if (this.pos.x < minX) { this.pos.x = minX; this.vel.x *= -BOUNCE; }
    if (this.pos.x > maxX) { this.pos.x = maxX; this.vel.x *= -BOUNCE; }
    if (this.pos.y < minY) { this.pos.y = minY; this.vel.y *= -BOUNCE; }
    if (this.pos.y > maxY) { this.pos.y = maxY; this.vel.y *= -BOUNCE; }
  }
  triggerArtIfFast() {
    let speed = this.vel.mag();
    if (!this.isSun && speed > SPEED_ART_THRESHOLD && this.cooldown === 0) {
      createArtwork(this.pos.copy(), this.vel.copy(), this.col, speed);
      this.cooldown = 25;
    }
  }
  show() {
    let n = noise(this.pos.x * 0.01, this.pos.y * 0.01, frameCount * 0.01);
    let b = map(n, 0, 1, 180, 255);
    noStroke();
    fill(hue(this.col), saturation(this.col), b, this.isSun ? 255 : 220);
    circle(this.pos.x, this.pos.y, this.isSun ? 22 : this.sz);
    stroke(hue(this.col), 180, 255, 80);
    strokeWeight(1);
    point(this.pos.x, this.pos.y);
  }
}

// --- Estallidos artísticos ---
function createArtwork(pos, vel, col, speed) {
  artworks.push({
    pos: pos.copy(),
    baseAngle: atan2(vel.y, vel.x),
    hue: hue(col),
    life: 60,
    size: map(speed, SPEED_ART_THRESHOLD, SPEED_ART_THRESHOLD * 2, 20, 60, true),
    spokes: floor(random(6, 12)),
    rotSpeed: random(-0.08, 0.08)
  });
  if (artworks.length > 120) artworks.splice(0, artworks.length - 120);
}
function renderArtworks() {
  // ya estamos con translate centrado
  for (let i = artworks.length - 1; i >= 0; i--) {
    let a = artworks[i];
    let t = a.life / 60;
    let alpha = 200 * t;
    push();
    translate(a.pos.x, a.pos.y);
    rotate(a.baseAngle + (1 - t) * TWO_PI * a.rotSpeed);
    stroke(a.hue, 200, 255, alpha);
    strokeWeight(1.6 * t + 0.4);
    noFill();
    for (let s = 0; s < a.spokes; s++) {
      let ang = (TWO_PI / a.spokes) * s;
      let r1 = a.size * (0.5 + 0.5 * t);
      let r2 = a.size * (1.2 + 0.4 * sin(frameCount * 0.2 + s));
      let x1 = cos(ang) * r1, y1 = sin(ang) * r1;
      let x2 = cos(ang) * r2, y2 = sin(ang) * r2;
      line(x1, y1, x2, y2);
      point(x2, y2);
    }
    pop();
    a.life--;
    if (a.life <= 0) artworks.splice(i, 1);
  }
}

// --- "Móviles" a lo Calder (pares cercanos) ---
function maybeCreateMobile(a, b) {
  // evita demasiados móviles activos
  if (mobiles.length > 40) return;

  let mid = p5.Vector.lerp(a.pos, b.pos, 0.5);
  let len = p5.Vector.dist(a.pos, b.pos);
  let baseAngle = atan2(b.pos.y - a.pos.y, b.pos.x - a.pos.x);

  // colores primarios Calder para el contrapeso
  let ornamentHue = random([0, 0, 0, 0,  50, 50,  220]); // sesgo a rojo/amarillo/azul
  let ornamentCol = color(ornamentHue, 220, 255);

  mobiles.push({
    aRef: a, bRef: b,             // siguen a las bolas
    life: 180 + floor(random(120)),
    baseAngle: baseAngle,
    len: len * 0.9,
    swayPhase: random(TWO_PI),
    swayAmp: random(0.06, 0.18),
    ornamentLen: random(22, 40),
    ornamentSize: random(8, 18),
    ornamentCol
  });
}

function renderMobiles() {
  for (let i = mobiles.length - 1; i >= 0; i--) {
    let m = mobiles[i];
    // actualizar geometría al seguir las partículas
    let a = m.aRef.pos;
    let b = m.bRef.pos;
    let mid = p5.Vector.lerp(a, b, 0.5);
    let dir = p5.Vector.sub(b, a);
    let baseAngle = atan2(dir.y, dir.x);
    let len = dir.mag() * 0.9;

    // oscilación suave (como brisa)
    let sway = sin(frameCount * 0.03 + m.swayPhase) * m.swayAmp;
    let angle = baseAngle + sway;

    // extremos de la barra
    let half = len * 0.5;
    let ex = cos(angle) * half;
    let ey = sin(angle) * half;
    let p1 = createVector(mid.x - ex, mid.y - ey);
    let p2 = createVector(mid.x + ex, mid.y + ey);

    // barra
    stroke(0, 0, 255, 200); // blanco (en HSB: H=0,S=0,B=255)
    strokeWeight(2);
    line(p1.x, p1.y, p2.x, p2.y);

    // pivotito central
    fill(0, 0, 255); // blanco
    noStroke();
    circle(mid.x, mid.y, 4);

    // varilla colgante y contrapeso de color primario
    let hangX = mid.x + cos(angle + HALF_PI) * m.ornamentLen;
    let hangY = mid.y + sin(angle + HALF_PI) * m.ornamentLen;
    stroke(0, 0, 255, 200);
    strokeWeight(1.2);
    line(mid.x, mid.y, hangX, hangY);

    noStroke();
    fill(hue(m.ornamentCol), saturation(m.ornamentCol), 255, 230);
    circle(hangX, hangY, m.ornamentSize);

    // vida
    m.life--;
    if (m.life <= 0) mobiles.splice(i, 1);
  }
}

// --- Utilidades de interfaz/HUD ---
function calderPalette(i) {
  // Paleta inspirada en Calder: rojo, amarillo, azul, negro, blanco
  const choices = [
    color(0,   220, 255),   // rojo
    color(50,  220, 255),   // amarillo
    color(220, 220, 255),   // azul
    color(0,     0,  40),   // negro profundo
    color(0,     0, 255)    // blanco
  ];
  return random(choices);
}

function screenToWorld(sx, sy) { return createVector(sx - width / 2, sy - height / 2); }
function mouseWheel(e) { G = constrain(G - e.delta * 0.001, 0.05, 3.0); }
function keyPressed() {
  if (key === 'c' || key === 'C') background(0);
  if (key === 'n' || key === 'N') useNoiseWind = !useNoiseWind;
  if (key === 'p' || key === 'P') paused = !paused;
  if (key === 'a' || key === 'A') calderMode = !calderMode;
}
function drawHUD() {
  fill(0, 140);
  noStroke();
  rect(12, 12, 320, 104, 10);
  fill(255);
  textSize(12);
  text(
    `N-cuerpos — Rebote + Estallidos + Calder
G (rueda): ${G.toFixed(2)} | Ruido (N): ${useNoiseWind ? 'ON' : 'OFF'} | Calder (A): ${calderMode ? 'ON' : 'OFF'}
C: limpiar  P: pausa  Arrastra: imán
Pelotas: ${NUM}  Móviles: ${mobiles.length}`,
    22, 34
  );
}
```
<img width="725" height="717" alt="image" src="https://github.com/user-attachments/assets/e45590a2-8de9-47e8-8d7f-12c6283c69f8" />

