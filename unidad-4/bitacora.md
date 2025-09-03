# Evidencias de la unidad 4

## Explicación conceptual de la obra

* ¿Qué concepto de la unidad 4 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
>

* ¿Qué concepto de la unidad 3 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
>

* ¿Qué concepto de la unidad 2 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
>

* ¿Qué concepto de la unidad 1 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
>

## ¿Cómo resolviste la interacción?
> Tu respuesta aquí:
>

## Enlace a la obra en el editor de p5.js

[Aquí está mi obra](https://editor.p5js.org/skulls562/full/LRgHNjt7O)

## Código de la obra 

``` js
let cancion, analAmp, analFFT;
let audioListo = false;

let baseY, A = 40, Avel = 0, Ak = 0.02, Adamp = 0.10;
let theta = 0, omegaBase = 0.02, tPerlin = 0;
let largoOndaBase = 200, largoOndaActual = 200;

const NBarras = 40;
let barras = [];
let tRuido = 0;

let impulso = 0, bajos = 0, medios = 0, agudos = 0;
let bajosS = 0, mediosS = 0, agudosS = 0;

let selector, lienzo;
let fuerzaRizo = 0;

const NPart = 140;
let particulas = [];

let ondas = [];

function setup() {
  pixelDensity(1);
  lienzo = createCanvas(900, 560);
  colorMode(HSL, 360, 100, 100, 1);
  baseY = height * 0.50;

  analAmp = new p5.Amplitude(); 
  analAmp.smooth(0.92);
  analFFT = new p5.FFT(0.85, 1024);

  selector = createFileInput(cargarCancion, false);
  selector.position(16, 14);
  selector.elt.accept = "audio/*";
  lienzo.drop(cargarCancion);

  const margenX = 40;
  const anchoUtil = width - margenX * 2;
  for (let i = 0; i < NBarras; i++) {
    const x = margenX + (i + 0.5) * (anchoUtil / NBarras);
    const l = map(noise(i * 0.15), 0, 1, height * 0.28, height * 0.38);
    barras.push(new Barra(x, 120, l, i));
  }

  for (let i = 0; i < NPart; i++) {
    particulas.push(new Particula(random(width), random(height), i));
  }
}

function cargarCancion(archivo) {
  if (!archivo) return;
  const fuente = archivo.file ? archivo.file : archivo;
  if (!fuente) return;
  if (cancion && cancion.isPlaying()) cancion.stop();
  loadSound(fuente, s => {
    cancion = s; 
    cancion.setVolume(1); 
    cancion.loop();
    analAmp.setInput(cancion);
    analFFT.setInput(cancion);
    audioListo = true;
  });
}

function draw() {
  leerAudio();
  background(0);

  tPerlin += 0.004;

  const dentro = mouseX >= 0 && mouseX <= width && mouseY >= 0 && mouseY <= height;
  const ctrlX = dentro ? constrain(mouseX / width, 0, 1) : 0.5;
  const ctrlY = dentro ? constrain(1 - mouseY / height, 0, 1) : 0.5;

  theta += (omegaBase + 0.03 * impulso) + map(noise(tPerlin), 0, 1, -0.0015, 0.0015);

  const Aobj = map(impulso, 0, 1, 18, 85, true) * (0.6 + 1.1 * pow(ctrlY, 1.2));
  const Aacc = Ak * (Aobj - A) - Adamp * Avel;
  Avel += Aacc; 
  A += Avel;

  largoOndaActual = lerp(largoOndaActual, map(impulso, 0, 1, largoOndaBase, 150), 0.05);

  fuerzaRizo = lerp(fuerzaRizo, 0, 0.06);

  actualizarOndas();

  for (let p of particulas) { p.actualizar(); p.dibujar(); }

  push(); dibujarEscena(ctrlX, ctrlY); pop();
  push(); translate(0, height); scale(1, -1); dibujarEscena(ctrlX, ctrlY); pop();

  hud();
}

function mousePressed(){ fuerzaRizo = 1.2; agregarOnda(mouseX, 95, 1.0); }
function mouseDragged(){ fuerzaRizo = 1.2; agregarOnda(mouseX, 95, 1.0); }

function leerAudio() {
  const nivel = audioListo ? analAmp.getLevel() : 0;
  impulso = constrain(nivel * 6.0, 0, 1);
  if (audioListo) {
    analFFT.analyze();
    bajos = analFFT.getEnergy("bass") / 255;
    medios = analFFT.getEnergy("mid") / 255;
    agudos = analFFT.getEnergy("treble") / 255;
  } else {
    bajos = medios = agudos = 0;
  }
  const a = 0.18;
  bajosS = lerp(bajosS, bajos, a);
  mediosS = lerp(mediosS, medios, a);
  agudosS = lerp(agudosS, agudos, a);
}

function yOla(x, ctrlX, ctrlY) {
  const k = TWO_PI / largoOndaActual;
  const bump = 0.45 * map(noise(0.003 * x + tPerlin * 0.6), 0, 1, -1, 1);
  const phi = theta + k * x + bump;
  const s1 = sin(phi);
  const mixNL = 0.75 * ctrlX + 0.35 * bajosS;
  const s1NL = lerp(s1, Math.tanh(3.0 * s1), constrain(mixNL, 0, 1));
  const s2 = (0.10 + 0.28 * mediosS + 0.35 * ctrlX) * sin(2 * phi);
  const s3 = (0.05 + 0.15 * agudosS + 0.18 * ctrlX * ctrlX) * sin(3 * phi + 0.8);
  let y = baseY + A * (0.7 + 1.3 * pow(ctrlY, 1.1)) * (s1NL + s2 + s3);
  let sum = 0;
  for (let o of ondas) {
    const dx = x - o.x0;
    const g = exp(-(dx * dx) / (2 * o.sigma * o.sigma));
    sum += o.fuerza * g * sin(phi * (1 + o.mod));
  }
  y += sum;
  const dxm = x - mouseX;
  const sig = 70 + 160 * (1 - ctrlY);
  const gm = exp(-(dxm * dxm) / (2 * sig * sig));
  const rz = fuerzaRizo * gm * sin((2.5 + 4 * ctrlX) * phi);
  y += 36 * (0.25 + 0.75 * impulso) * rz;
  return y;
}

function dibujarEscena(ctrlX, ctrlY) {
  const paso = 5;
  const grosor = 1.5 + 3.5 * impulso;
  strokeWeight(grosor);
  noFill();
  for (let x = 0; x < width - paso; x += paso) {
    const y1 = yOla(x, ctrlX, ctrlY);
    const y2 = yOla(x + paso, ctrlX, ctrlY);
    const h = (200 + 120 * mediosS + 180 * ctrlX + 80 * agudosS) % 360;
    const s = 65 + 30 * max(mediosS, ctrlX);
    const l = 48 + 12 * max(bajosS, ctrlY);
    stroke(h, s, l, 0.95);
    line(x, y1, x + paso, y2);
  }

  tRuido += 0.01;
  for (let i = 0; i < NBarras; i++) barras[i].actualizar(impulso, tRuido);

  beginShape();
  strokeWeight(2.5);
  const h2 = (220 + 80 * mediosS + 160 * ctrlX + 60 * agudosS) % 360;
  stroke(h2, 60 + 30 * agudosS, 44 + 16 * impulso, 0.85);
  noFill();
  for (let i = 0; i < NBarras; i++) {
    const p = barras[i].punta();
    curveVertex(p.x, p.y);
  }
  endShape();

  for (let i = 0; i < NBarras; i++) barras[i].dibujar();
}

class Barra {
  constructor(xAnclaje, yTop, largoBase, indice) {
    this.x0 = xAnclaje; this.yTop = yTop; this.largoBase = largoBase; this.indice = indice;
    this.theta = map(noise(indice * 0.2), 0, 1, -0.05, 0.05);
    this.omega = 0; this.alpha = 0;
    this.k = 0.035; this.c = 0.06;
    this.h = 200; this.y0 = 0; this.L = this.largoBase;
  }
  actualizar(imp, tN) {
    this.y0 = yOla(this.x0, 0.5, 0.5);
    const addL = map(noise(tN * 0.35 + this.indice * 0.27), 0, 1, -10, 10);
    const addDrive = map(imp, 0, 1, 0, 26);
    this.L = max(24, this.largoBase + addL + addDrive);
    const tAudio = 0.022 * imp * sin(frameCount * 0.045 + this.indice * 0.4);
    this.alpha = -this.k * this.theta - this.c * this.omega + tAudio;
    this.omega += this.alpha; this.theta += this.omega;
    this.h = (map(noise(0.5 * this.indice + tN), 0, 1, 185, 315) + 60 * mediosS + 40 * agudosS) % 360;
  }
  punta() {
    const x1 = this.x0, y1 = this.y0;
    const x2 = x1 + this.L * sin(this.theta);
    const y2 = y1 + this.L * cos(this.theta);
    return createVector(x2, y2);
  }
  dibujar() {
    const p = this.punta();
    strokeWeight(3.5);
    stroke(this.h, 80 + 15 * agudosS, 55 + 10 * impulso, 0.18);
    line(this.x0 + 2, this.y0 + 2, p.x + 2, p.y + 2);
    stroke(this.h, 80 + 15 * agudosS, 55 + 10 * impulso, 0.95);
    line(this.x0, this.y0, p.x, p.y);
  }
}

class Particula {
  constructor(x, y, i) {
    this.pos = createVector(x, y);
    const ang = random(TWO_PI);
    const vel = random(0.6, 1.6);
    this.vel = p5.Vector.fromAngle(ang).mult(vel);
    this.i = i;
    this.r = random(1.5, 3.0);
  }
  actualizar() {
    const velAudio = 1 + 0.8 * impulso;
    this.pos.add(p5.Vector.mult(this.vel, velAudio));

    if (this.pos.x < 0) { this.pos.x = 0; this.vel.x *= -1; }
    if (this.pos.x > width) { this.pos.x = width; this.vel.x *= -1; }
    if (this.pos.y < 0) { this.pos.y = 0; this.vel.y *= -1; }
    if (this.pos.y > height) { this.pos.y = height; this.vel.y *= -1; }

    const yWave = yOla(this.pos.x, 0.5, 0.5);
    if (abs(this.pos.y - yWave) < 10) {
      if (this.pos.y > yWave) { this.vel.y = abs(this.vel.y); }
      else { this.vel.y = -abs(this.vel.y); }
      const energia = 30 * (abs(this.vel.x) + abs(this.vel.y));
      agregarOnda(this.pos.x, 70, constrain(energia / 40, 0.4, 1.0));
    }
  }
  dibujar() {
    const h = (200 + 120 * mediosS + 160 * agudosS + 4 * this.i) % 360;
    noStroke();
    fill(h, 80, 60, 0.75);
    circle(this.pos.x, this.pos.y, this.r * (1 + 0.6 * impulso));
  }
}

function agregarOnda(x0, sigma, fuerza) {
  ondas.push({ x0, sigma, fuerza: 28 * fuerza, mod: random(0.2, 0.8) });
}

function actualizarOndas() {
  for (let o of ondas) {
    o.fuerza *= 0.94;
    o.sigma *= 1.01;
  }
  ondas = ondas.filter(o => o.fuerza > 0.8);
}

function hud() {
  noStroke();
  fill(0, 0, 100, 0.05);
  rect(12, 12, 560, 86, 8);
  fill(0, 0, 100, 0.9);
  textSize(12);
  text("Sube tu canción (MP3/WAV) o arrástrala aquí • Clic/arrastra para “golpear” la ola", 20, 24);
  text("Partículas: " + NPart + " • Intensidad: " + nf(impulso, 1, 2), 20, 44);
}

function keyPressed(){ if(key==='s'||key==='S'){ saveCanvas('captura','png'); } }

```

## Captura de pantalla representativa


<img width="913" height="569" alt="image" src="https://github.com/user-attachments/assets/6242f558-a3fb-4149-b515-44c670dc5bb5" />





