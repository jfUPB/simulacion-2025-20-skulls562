# Evidencias de la unidad 6

# Actividad 1

Tyler Hobs

Imagen 1

<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/83fcc313-6bb6-4526-9dac-711757259c44" />

-¿Por qué me llama la atención?

Me gusta la dirección diagonal que crea una sensación de flujo continuo y me gustan las bandas de color que mantienen su identidad aunque se curvan.

-Lo que me inspira de esta imagen

Me inspira bastante el trabajar con streamlines largas que compartan el mismo camino.

Imagen 2

<img width="1920" height="1920" alt="image" src="https://github.com/user-attachments/assets/cf887cba-3e21-40ff-97d9-e474b83ff215" />

-¿Por qué me llama la atención?

Me gusta la textura tipo grafito/grabado que aparece con miles de trazos y tambien me gusta el contraste entre turbulencia local y dirección global.

-Lo que me inspira de esta imagen

Me inspira sumar curl noise para remolinos y bordes vivos y tambien me inspira usar trazos muy finos con baja opacidad para construir valor tonal.


# Actividad 2

Para mí, la steering force es una fuerza de guiado (de control) que uso para corregir mi velocidad actual y llevarla hacia una velocidad deseada. Matemáticamente la pienso así:

Deseado: un vector objetivo (depende del comportamiento).

Steer: steer = desired - velocity

Luego limito steer a maxforce y actualizo como aceleración (y la velocidad se limita con maxspeed).

Ejemplos rápidos de “deseado”:

Seek (ir a un objetivo): desired = normalize(target - pos) * maxspeed.

Arrive (frenar cerca): igual que seek pero reduciendo la magnitud del desired cuando estoy a poca distancia.

Flow field: desired = lookupCampo(pos) * maxspeed.

2) ¿En qué se diferencia de las fuerzas “físicas” que ya vimos?

Así lo entiendo y lo uso:

Intención: las fuerzas físicas (gravedad, rozamiento, viento, resortes, atracción) vienen del mundo; la steering viene de una decisión del agente (objetivo, regla o campo).

Cálculo: físicas = fórmulas basadas en posición/velocidad y constantes del entorno; steering = control “deseado − velocidad”, con clamps (maxforce, maxspeed) para estabilidad.

Teleología: físicas producen movimiento “emergente” pero no necesariamente orientado a metas; steering es goal-directed (seguir, evitar, alinearse, llegar).

Percepción local: en steering leo vecinos o un campo y mezclo varias fuerzas con pesos (ej., separación/alineación/cohesión). Las físicas no suelen combinarse con pesos “conductuales”.

Estabilidad/arte: al limitar maxforce/maxspeed, la steering me da curvas suaves y controlables; con solo fuerzas físicas es más difícil garantizar respuestas “creíbles” y suaves.

3) Relación con Craig Reynolds

Reynolds es la base de todo esto:

En “Boids” (1987) formaliza el movimiento colectivo con tres reglas locales (separación, alineación, cohesión). Cada regla genera una steering force; yo las sumo con pesos y limito.

En “Steering Behaviors for Autonomous Characters” (1999) cataloga comportamientos como seek, flee, arrive, wander, pursue/evade, path/flow following, obstacle avoidance, etc. Todos se implementan como variantes del mismo patrón: calcular un desired y convertirlo en steer limitado.

En resumen: steering es el marco de control que hace posible Boids y la animación conductual moderna; yo lo aplico igual en flow fields, flocking y navegación con obstáculos.



# Actividad 3

- Estructura del campo

Uso una cuadrícula con celdas de tamaño cellSize; guardo los vectores en un array 1D field[x + y*cols].

Cada celda almacena un p5.Vector unitario (dirección del flujo).

Genero el ángulo por noise (Perlin) y lo convierto con p5.Vector.fromAngle.


-Cómo sigo el campo (follow + steering)


Mapeo mi posición a la celda: gx=floor(x/cellSize), gy=floor(y/cellSize).

Tomo el vector de esa celda como deseado, lo pongo a maxspeed.

Calculo steer = desired - vel, lo limito a maxforce y lo aplico como aceleración.


- Parámetros clave


Resolución: cellSize.

Dinámica: maxspeed, maxforce.

Ruido: noiseScale (detalle) y noiseZInc (animación).


- Modificación que hice


Reemplacé Perlin por curl noise (vector tangente a las isolíneas del ruido).

Efecto observado: aparecen remolinos y rutas cerradas; los agentes forman bandas curvas más ricas. Con maxforce bajo, las curvas son suaves; con maxforce alto, reaccionan más brusco.



<img width="900" height="567" alt="image" src="https://github.com/user-attachments/assets/d202214f-4176-4bd8-adf9-c0f81175c704" />


Codgo modficado


```js

const dx = noise(xoff + eps, yoff, z) - noise(xoff - eps, yoff, z);
const dy = noise(xoff, yoff + eps, z) - noise(xoff, yoff - eps, z);
let v = createVector(dy, -dx).normalize();
this.field[this.index(x, y)] = v;

```

# Acividad 4

- Reglas

Separación: quiero evitar hacinamiento.
Cómo la calculo: miro vecinos muy cercanos; por cada uno sumo un vector desde el vecino hacia mí, normalizado e inverso a la distancia; promedio y lo convierto en steer (limito a maxforce).

Alineación: quiero moverme con la dirección promedio de mis vecinos.
Cómo la calculo: promedio sus velocidades; ese promedio lo llevo a maxspeed y hago desired - vel, limitando por maxforce.

Cohesión: quiero ir hacia el centro de masa del grupo.
Cómo la calculo: promedio sus posiciones; apunto del centro hacia mí con un seek (mismo patrón: desired - vel, limitado).


- Parámetros clave


Percepción: perceptionRadius (quién es vecino).

Pesos: cuánto pesa cada regla al combinarlas (ej. wSep, wAli, wCoh).

Límites dinámicos: maxspeed, maxforce.


- Modificación que hice


Cohesión = 0 (pongo wCoh = 0): el enjambre pierde tendencia a agruparse; veo bandas sueltas y dispersión, sobre todo si aumento wSep.

Percepción muy grande (ej. 120 px): los boids se alinean globalmente y tienden a formar grandes cardúmenes con menos detalle local.


<img width="894" height="554" alt="image" src="https://github.com/user-attachments/assets/8565297f-c186-4e3e-9b1a-fd19d39861fa" />


```js
W.coh = 0.0; // apago cohesión para observar dispersión
```


# Actividad 5 (apply)


- Tema musical

Elegí Infinite Azure de Rojuu. Me gusta porque combina un bajo 808 profundo con atmósferas etéreas; esa dualidad (peso + aire) encaja perfecto con flow fields y flocking.



- Idea / concepto

Quiero un instrumento visual que “toque” el bajo y a la vez pinte el espacio con colores azules/celestes que nacen de la portada. Los agentes siguen un flow field deformado por la imagen y, en otra escena, se comportan como cardúmenes (flocking). La música controla su comportamiento en tiempo real.


- Mapeo audio → visual


Graves (808): engrosan trazos y aceleran el campo; en flocking suben cohesión.

Medios (voz/sintes): aumentan fuerza de giro (flow) y alineación (flock).

Agudos (hi-hats/brillos): dan brillo/alpha, más separación y destellos/chispeos.



- Interacción que yo “tocó”


ESPACIO play/pause. Escenas: 1 Flow / 2 Flock.

Bajo-teclado: Z..M, A..J, Q..U mueven un band-pass sobre la canción (suena y además dispara destellos aleatorios por el canvas). SHIFT = stutter.

FX: 3 Delay, 4 Reverb, 5 Distorsión. Rate: -/= y 0.

Portada: I influye el campo; T activa emisores desde sus bordes.

Macros: K shockwave, L ribbon-burst. S guarda captura.



Imagen 



<img width="1138" height="716" alt="image" src="https://github.com/user-attachments/assets/e0474bd7-8b77-4b21-a533-4d8c2f1823c7" />



Link : https://editor.p5js.org/skulls562/sketches/PfdRAARsS




Codigo 


```js
/* APPLY — PRO FINAL
   Teclas modifican la canción + visuales avanzados (Flow/Flock) + portada
   ---------------------------------------------------------------
   SPACE: Play/Pause   - / = : Rate down/up   0: Rate 1.0
   3: Delay  4: Reverb  5: Distortion
   [: Q- band-pass   ]: Q+ band-pass
   ,: Vol- canal bajo   .: Vol+ canal bajo
   O: LOOP MODE ON/OFF   (1..9 fija slice; P limpia loop)

   "Bajo teclado" (band-pass + golpe):
   Z X C V B N M (graves) · A S D F G H J (medios) · Q W E R T Y U (altos)
   SHIFT (hold): stutter del canal de bajo

   Visuales:
   1: Flow Field  ·  2: Flocking
   B: Bass Sculpt ON/OFF   I: Portada influye campo ON/OFF
   T: Edge-emitters ON/OFF (desde bordes de la portada)
   K: Shockwave en mouse   L: Ribbon-burst en mouse
   H: HUD ON/OFF   S: Snapshot PNG
   ←/→: cellSize  ↑/↓: #agents(flow)  Q/W: maxSpeed(flow)  A/S: maxForce(flow)
   Z/X: perception(flock)  E/R: maxForce(flock)  D/F: maxSpeed(flock)
*/

let mode = "flow";
let bassSculpt = true;
let imageInfluence = true;
let showHUD = true;
let edgeEmittersOn = false;

let FX = { delayOn:false, revOn:false, distOn:false };
let playRate = 1.0;

let song, fft, amp, peak;
let audioInput, coverInput;
let audioLoaded = false;

// Portada
let coverImg = null, coverBuf = null, coverPixels = null, edgeMask = null;

// Capas
let pgTrails; // buffer de estelas

// Flow Field
const FCFG = {
  cellSize: 18,
  noiseScale: 0.006,
  noiseZInc: 0.002,
  maxSpeed: 2.8,
  maxForce: 0.08,
  count: 560,
  imageWeight: 0.6
};
let field, agents = [];
let zoff = 0;

// Flocking
const BCFG = {
  count: 280,
  maxSpeed: 2.5,
  maxForce: 0.06,
  perception: 65,
  W: { sep: 1.6, ali: 1.0, coh: 0.95 }
};
let boids = [];

// AUDIO routing
let dryGain, bassFilter, bassGain, delayFX, reverbFX, distFX;
let bassBase = 0.12;   // nivel base canal bajo
let bassBoost = 0.85;  // golpe al pulsar notas
let bassCenterHz = 95; // centro del band-pass
let bassQ = 4.0;
let stutterOn = false;

// LOOP slicing
let loopMode = false, loopActive = false, loopStart = 0, loopEnd = 0;

// Eventos visuales
let shockwaves = [];
let ribbons = [];
let bursts = [];

function setup() {
  createCanvas(1200, 720);
  pixelDensity(1);
  textFont('monospace');

  // buffer de estelas
  pgTrails = createGraphics(width, height);
  pgTrails.pixelDensity(1);
  pgTrails.background(10);

  // Audio analysis
  fft  = new p5.FFT(0.9, 1024);
  amp  = new p5.Amplitude(0.9);
  peak = new p5.PeakDetect(20, 15000, 0.12, 30);

  // Inputs
  createP("").style("margin","6px");
  createSpan("🎵 Cargar canción: ").style("color","#ddd");
  audioInput = createFileInput(handleAudioFile); audioInput.attribute("accept","audio/*");
  createSpan("   🖼️ Portada: ").style("color","#ddd");
  coverInput = createFileInput(handleCoverFile); coverInput.attribute("accept","image/*");

  // Flow init
  field = new FlowField(FCFG.cellSize);
  for (let i = 0; i < FCFG.count; i++) agents.push(new Vehicle(random(width), random(height)));

  // Flock init
  for (let i = 0; i < BCFG.count; i++) boids.push(new Boid(random(width), random(height)));
}

function draw() {
  background(6);

  // desvanecer trails
  pgTrails.noStroke(); pgTrails.fill(0, 22); pgTrails.rect(0, 0, width, height);

  // --- Audio (bandas) ---
  if (audioLoaded) peak.update(fft);

  const eBass = fft.getEnergy('bass')   / 255;   // graves
  const eMid  = fft.getEnergy('mid')    / 255;   // medios
  const eHigh = fft.getEnergy('treble') / 255;   // agudos
  const level = amp.getLevel();

  // picos globales -> macros automáticas
  if (peak.isDetected) {
    spawnShockwave(random(width), random(height), 18 + 60*eBass);
    spawnRibbonBurst(random(width), random(height), 10 + 40*eBass);
  }

  // escenas usando 3 bandas
  if (mode === "flow") drawFlow(eBass, eMid, eHigh, level);
  else drawFlock(eBass, eMid, eHigh, level);

  // emisores desde bordes según agudos
  if (edgeEmittersOn && edgeMask) emitFromEdges(eHigh);

  // glitter por agudos
  if (random() < eHigh * 0.18) bursts.push(new Burst(random(width), random(height), 12, 2.4, 18));

  // Dibujar macros (shock/ribbon/burst)
  for (let i = shockwaves.length-1; i >= 0; i--) {
    shockwaves[i].update(); shockwaves[i].draw(pgTrails);
    if (shockwaves[i].dead) shockwaves.splice(i,1);
  }
  for (let i = ribbons.length-1; i >= 0; i--) {
    ribbons[i].update(field); ribbons[i].draw(pgTrails);
    if (ribbons[i].dead) ribbons.splice(i,1);
  }
  for (let i = bursts.length-1; i >= 0; i--) {
    bursts[i].update(); bursts[i].draw(pgTrails);
    if (bursts[i].dead) bursts.splice(i,1);
  }

  // loop slicing
  if (loopActive && song && song.isPlaying() && song.currentTime() > loopEnd) song.jump(loopStart);

  // Componer
  image(pgTrails, 0, 0);

  if (showHUD) drawHUD(eBass, eMid, eHigh, level);
}

/* ---------------- FLOW RENDER ---------------- */
function drawFlow(bassN, midN, highN, level) {
  if (imageInfluence && coverBuf) field.generatePerlinWithImage(FCFG.noiseScale, zoff, FCFG.imageWeight);
  else field.generatePerlin(FCFG.noiseScale, zoff);

  zoff += FCFG.noiseZInc * (1.0 + bassN*1.0 + midN*0.6);

  const maxSpeed = FCFG.maxSpeed * (1.0 + (bassSculpt ? bassN*0.8 : 0));
  const maxForce = FCFG.maxForce * (1.0 + (bassSculpt ? midN*0.6  : 0));

  for (const a of agents) {
    a.maxspeed = maxSpeed; a.maxforce = maxForce;
    a.follow(field); a.update(); a.edges();

    let alpha = 120 + highN*135;
    let col = coverPixels ? sampleCoverColor(a.pos.x, a.pos.y, alpha) : color(235, alpha);
    pgTrails.stroke(col);
    pgTrails.strokeWeight(0.9 + bassN*1.1);
    pgTrails.line(a.prev.x, a.prev.y, a.pos.x, a.pos.y);
    a.prev.set(a.pos);
  }
}

/* --------------- FLOCK RENDER ---------------- */
function drawFlock(bassN, midN, highN, level) {
  // cohesión ↔ bajos, alineación ↔ medios, separación ↔ agudos
  let sepW = BCFG.W.sep * (1.0 + highN*0.9);
  let aliW = BCFG.W.ali * (1.0 + midN*0.8);
  let cohW = BCFG.W.coh * (1.0 + bassN*1.2);

  for (const b of boids) {
    b.maxspeed = BCFG.maxSpeed * (1.0 + bassN*0.4 + midN*0.2);
    b.maxforce = BCFG.maxForce;
    b.flock(boids, sepW, aliW, cohW, BCFG.perception);
    b.update(); b.edges();

    let alpha = 160 + highN*90;
    let col = coverPixels ? sampleCoverColor(b.pos.x, b.pos.y, alpha) : color(240, alpha);
    pgTrails.noStroke(); pgTrails.fill(col);
    b.render(pgTrails);
  }
}

/* ------------ EDGE EMITTERS (portada) -------- */
function emitFromEdges(highN){
  if (!edgeMask) return;
  const tries = 20 + floor(highN * 200); // más agudos -> más emisores
  for (let i = 0; i < tries; i++) {
    const x = floor(random(width)), y = floor(random(height));
    const idx = x + y*width;
    if (edgeMask[idx]) {
      const col = sampleCoverColor(x, y, 210);
      bursts.push(new Burst(x, y, 12 + floor(random(8)), 2.2 + random(0.8), 26 + floor(random(10)), col));
    }
  }
}

/* ---------------- AUDIO ROUTING --------------- */
function configureAudioRouting() {
  song.disconnect();

  dryGain = new p5.Gain(); dryGain.setInput(song); dryGain.amp(0.85); dryGain.connect();

  // Band-pass canal "bajo"
  bassFilter = new p5.Filter('bandpass'); bassFilter.res(bassQ); bassFilter.freq(bassCenterHz);
  bassGain   = new p5.Gain(); bassGain.amp(bassBase);
  song.connect(bassFilter); bassFilter.connect(bassGain); bassGain.connect();

  // FX paralelos (inician apagados)
  delayFX = new p5.Delay();  delayFX.process(song, 0.24, 0.35, 1800); delayFX.amp(0.0);
  reverbFX = new p5.Reverb(); reverbFX.process(song, 2.8, 2.5);        reverbFX.amp(0.0);
  distFX = new p5.Distortion(0.3); distFX.process(song);               distFX.amp(0.0);

  playRate = 1.0; song.rate(playRate);
  FX = { delayOn:false, revOn:false, distOn:false };
}

/* --------------- KEY → FREQ MAP --------------- */
const KEY_FREQ = {
  'z': 82.41,  'x': 92.50,  'c': 98.00,  'v': 110.00, 'b': 123.47, 'n': 146.83, 'm': 164.81,
  'a': 87.31,  's': 97.99,  'd': 103.83, 'f': 116.54, 'g': 130.81, 'h': 155.56, 'j': 174.61,
  'q': 130.81, 'w': 146.83, 'e': 164.81, 'r': 174.61, 't': 196.00, 'y': 220.00, 'u': 246.94
};

function triggerBassKey(freq){
  if (!bassFilter || !bassGain) return;
  bassCenterHz = freq;
  bassFilter.freq(bassCenterHz, 0.04);

  // golpe de “bajo”
  bassGain.amp(bassBase + bassBoost, 0.015);
  bassGain.amp(bassBase + 0.2, 0.12, 0.06); // decay

  // DESTELLOS AL AZAR por el canvas
  const n = floor(random(5, 12));
  for (let i = 0; i < n; i++) {
    const x = random(width), y = random(height);
    spawnShockwave(x, y, random(22, 42));
    spawnRibbonBurst(x, y, floor(random(24, 50)));
    if (random() < 0.6) spawnBurst(x, y, random(18, 34));
  }
}

/* -------------------- INPUTS ------------------ */
function handleAudioFile(file){
  if (file && file.type === 'audio') {
    if (song && song.isPlaying()) song.stop();
    song = loadSound(file.data, () => {
      audioLoaded = true; configureAudioRouting(); song.play(); userStartAudio();
    });
  }
}
function handleCoverFile(file){
  if (file && file.type === 'image') {
    loadImage(file.data, img => { coverImg = img; buildCoverBuffer(); });
  }
}
function buildCoverBuffer(){
  coverBuf = createGraphics(width, height);
  coverBuf.push(); coverBuf.background(0);
  const cw=coverImg.width, ch=coverImg.height, sc=Math.max(width/cw, height/ch);
  const w=cw*sc, h=ch*sc, x=(width-w)/2, y=(height-h)/2;
  coverBuf.image(coverImg, x, y, w, h);
  coverBuf.pop(); coverBuf.loadPixels(); coverPixels = coverBuf.pixels;

  // edge mask (gradiente simple)
  edgeMask = new Array(width*height).fill(0);
  for (let y=1; y<height-1; y++){
    for (let x=1; x<width-1; x++){
      const i = 4*(x + y*width);
      const l = lumAt(i);
      const lx = lumAt(i-4), rx = lumAt(i+4), uy = lumAt(i-4*width), dy = lumAt(i+4*width);
      const gx = Math.abs(rx - lx), gy = Math.abs(dy - uy);
      edgeMask[x + y*width] = (gx+gy) > 60 ? 1 : 0;
    }
  }
  function lumAt(idx){ const r=coverPixels[idx], g=coverPixels[idx+1], b=coverPixels[idx+2]; return 0.299*r+0.587*g+0.114*b; }
}
function sampleCoverColor(x, y, alpha=200){
  if (!coverPixels) return color(220, alpha);
  const xi = constrain(floor(x), 0, width-1), yi = constrain(floor(y), 0, height-1);
  const idx = 4 * (xi + yi * width);
  return color(coverPixels[idx], coverPixels[idx+1], coverPixels[idx+2], alpha);
}

/* ---------------- FLOW FIELD ------------------ */
class FlowField {
  constructor(res){ this.resize(res); }
  resize(res){
    this.res = res;
    this.cols = floor(width / res) + 1;
    this.rows = floor(height / res) + 1;
    this.field = new Array(this.cols * this.rows);
  }
  index(x,y){ return x + y * this.cols; }
  lookup(pos){
    const x = constrain(floor(pos.x / this.res), 0, this.cols-1);
    const y = constrain(floor(pos.y / this.res), 0, this.rows-1);
    return this.field[this.index(x,y)].copy();
  }
  generatePerlin(scale, z){
    let yoff = 0;
    for (let y = 0; y < this.rows; y++) {
      let xoff = 0;
      for (let x = 0; x < this.cols; x++) {
        const theta = noise(xoff, yoff, z) * TWO_PI * 2 - PI;
        this.field[this.index(x,y)] = p5.Vector.fromAngle(theta);
        xoff += scale;
      }
      yoff += scale;
    }
  }
  generatePerlinWithImage(scale, z, imgWeight=0.6){
    let yoff = 0;
    for (let y = 0; y < this.rows; y++) {
      let xoff = 0;
      for (let x = 0; x < this.cols; x++) {
        let theta = noise(xoff, yoff, z) * TWO_PI * 2 - PI;
        if (coverPixels){
          const px = constrain(floor(x*this.res + this.res*0.5), 0, width-1);
          const py = constrain(floor(y*this.res + this.res*0.5), 0, height-1);
          const i = 4 * (px + py * width);
          const r = coverPixels[i], g = coverPixels[i+1], b = coverPixels[i+2];
          const lum = 0.299*r + 0.587*g + 0.114*b;
          const offset = map(lum, 0, 255, -PI/2.6, PI/2.6) * imgWeight;
          theta += offset;
        }
        this.field[this.index(x,y)] = p5.Vector.fromAngle(theta);
        xoff += scale;
      }
      yoff += scale;
    }
  }
}

/* ---------------- VEHICLE (Flow) -------------- */
class Vehicle {
  constructor(x,y){
    this.pos = createVector(x,y);
    this.vel = p5.Vector.random2D();
    this.acc = createVector();
    this.maxspeed = FCFG.maxSpeed;
    this.maxforce = FCFG.maxForce;
    this.prev = this.pos.copy();
  }
  applyForce(f){ this.acc.add(f); }
  follow(flow){
    const desired = flow.lookup(this.pos).setMag(this.maxspeed);
    const steer = p5.Vector.sub(desired, this.vel).limit(this.maxforce);
    this.applyForce(steer);
  }
  update(){ this.vel.add(this.acc).limit(this.maxspeed); this.pos.add(this.vel); this.acc.mult(0); }
  edges(){
    if (this.pos.x < 0) this.pos.x = width;
    if (this.pos.x > width) this.pos.x = 0;
    if (this.pos.y < 0) this.pos.y = height;
    if (this.pos.y > height) this.pos.y = 0;
    this.prev.set(this.pos);
  }
}

/* ----------------- BOID (Flocking) ------------ */
class Boid {
  constructor(x,y){ this.pos=createVector(x,y); this.vel=p5.Vector.random2D().mult(random(0.5,2)); this.acc=createVector(); this.maxspeed=BCFG.maxSpeed; this.maxforce=BCFG.maxForce; }
  applyForce(f){ this.acc.add(f); }
  flock(boids, wSep,wAli,wCoh, perception){
    const sep = this.separation(boids, perception*0.6).mult(wSep);
    const ali = this.alignment(boids, perception).mult(wAli);
    const coh = this.cohesion(boids, perception).mult(wCoh);
    this.applyForce(sep); this.applyForce(ali); this.applyForce(coh);
  }
  separation(boids, r){ let steer=createVector(), total=0; for(const o of boids){ const d=p5.Vector.dist(this.pos,o.pos); if(o!==this&&d<r&&d>0){ let diff=p5.Vector.sub(this.pos,o.pos).normalize().div(d); steer.add(diff); total++; } } if(total){ steer.div(total).setMag(this.maxspeed).sub(this.vel).limit(this.maxforce);} return steer; }
  alignment(boids, r){ let avg=createVector(), total=0; for(const o of boids){ const d=p5.Vector.dist(this.pos,o.pos); if(o!==this&&d<r){ avg.add(o.vel); total++; } } if(total){ avg.div(total).setMag(this.maxspeed).sub(this.vel).limit(this.maxforce);} return avg; }
  cohesion(boids, r){ let center=createVector(), total=0; for(const o of boids){ const d=p5.Vector.dist(this.pos,o.pos); if(o!==this&&d<r){ center.add(o.pos); total++; } } if(total){ center.div(total); const desired=p5.Vector.sub(center,this.pos).setMag(this.maxspeed); return desired.sub(this.vel).limit(this.maxforce);} return createVector(); }
  update(){ this.vel.add(this.acc).limit(this.maxspeed); this.pos.add(this.vel); this.acc.mult(0); }
  edges(){ if(this.pos.x<0)this.pos.x=width; if(this.pos.x>width)this.pos.x=0; if(this.pos.y<0)this.pos.y=height; if(this.pos.y>height)this.pos.y=0; }
  render(pg){ const th=this.vel.heading()+PI/2; pg.push(); pg.translate(this.pos.x,this.pos.y); pg.rotate(th); pg.beginShape(); pg.vertex(0,-6); pg.vertex(-4,6); pg.vertex(4,6); pg.endShape(CLOSE); pg.pop(); }
}

/* ----------------- MACROS VISUALES ------------ */
class Shockwave {
  constructor(x,y,size){ this.p=createVector(x,y); this.r=1; this.thick=8+size*0.2; this.alpha=220; this.dead=false; }
  update(){ this.r += 10; this.alpha *= 0.94; if(this.alpha<8) this.dead=true; }
  draw(pg){ pg.noFill(); const c=coverPixels? sampleCoverColor(this.p.x,this.p.y,this.alpha):color(255,this.alpha); pg.stroke(c); pg.strokeWeight(this.thick*(this.alpha/220)); pg.circle(this.p.x,this.p.y,this.r*2); }
}
class RibbonParticle {
  constructor(x,y){ this.p=createVector(x,y); this.v=p5.Vector.random2D().mult(random(0.8,2.2)); this.life=90+floor(random(60)); this.prev=this.p.copy(); }
  update(flow){
    const desired = flow.lookup(this.p).setMag(2.2);
    this.v.add(p5.Vector.sub(desired,this.v).limit(0.12));
    this.p.add(this.v); this.life--; this.prev.set(this.p);
    if(this.p.x<0)this.p.x=width; if(this.p.x>width)this.p.x=0; if(this.p.y<0)this.p.y=height; if(this.p.y>height)this.p.y=0;
  }
  draw(pg){ let col = coverPixels? sampleCoverColor(this.p.x,this.p.y,200) : color(255,200); pg.stroke(col); pg.strokeWeight(1.6); pg.line(this.prev.x,this.prev.y,this.p.x,this.p.y); }
}
class RibbonBurst {
  constructor(x,y,count){ this.ps=[]; for(let i=0;i<count;i++) this.ps.push(new RibbonParticle(x + random(-12,12), y + random(-12,12))); this.dead=false; }
  update(flow){ let alive=false; for(const p of this.ps){ if(p.life>0){ p.update(flow); alive=true; } } this.dead=!alive; }
  draw(pg){ for(const p of this.ps){ if(p.life>0) p.draw(pg); } }
}
class Burst {
  constructor(x,y,count=40,spd=2.2,life=28,col){
    this.ps=[];
    for(let i=0;i<count;i++){
      const a=random(TWO_PI);
      const v=createVector(cos(a),sin(a)).mult(spd*random(0.3,1.2));
      const p=createVector(x,y);
      const c=col || (coverPixels? sampleCoverColor(x,y,200) : color(255,200));
      this.ps.push({p,v,life:life+random(-8,10),col:c});
    }
    this.dead=false;
  }
  update(){ let alive=false; for(const prt of this.ps){ if(prt.life>0){ prt.v.mult(0.985); prt.p.add(prt.v); prt.life--; alive=true; } } this.dead=!alive; }
  draw(pg){ for(const prt of this.ps){ if(prt.life>0){ pg.stroke(prt.col); pg.strokeWeight(1); pg.point(prt.p.x,prt.p.y); } } }
}
function spawnShockwave(x,y,size=24){ shockwaves.push(new Shockwave(x,y,size)); }
function spawnRibbonBurst(x,y,count=28){ ribbons.push(new RibbonBurst(x,y, count)); }
function spawnBurst(x,y,size=24){ bursts.push(new Burst(x,y, 35+size, 2.2+size*0.02, 28+size*0.1)); }

/* -------------------- KEYS -------------------- */
function keyPressed(){
  // transporte
  if (key === ' ') { userStartAudio(); if (song){ song.isPlaying()? song.pause(): song.play(); } }

  // escenas / toggles
  if (key === '1') mode = "flow";
  if (key === '2') mode = "flock";
  if (key === 'B' || key === 'b') bassSculpt = !bassSculpt;
  if (key === 'I' || key === 'i') imageInfluence = !imageInfluence;
  if (key === 'T' || key === 't') edgeEmittersOn = !edgeEmittersOn;
  if (key === 'H' || key === 'h') showHUD = !showHUD;


  // FLOW tweaks
  if (keyCode === LEFT_ARROW)  { FCFG.cellSize = max(8, FCFG.cellSize-2); field.resize(FCFG.cellSize); }
  if (keyCode === RIGHT_ARROW) { FCFG.cellSize += 2; field.resize(FCFG.cellSize); }
  if (keyCode === UP_ARROW)    { agents.push(new Vehicle(random(width), random(height))); }
  if (keyCode === DOWN_ARROW)  { if (agents.length>60) agents.pop(); }
  if (key === 'Q') FCFG.maxSpeed = max(0.6, FCFG.maxSpeed-0.2);
  if (key === 'W') FCFG.maxSpeed += 0.2;
  if (key === 'A') FCFG.maxForce = max(0.01, FCFG.maxForce-0.01);
  if (key === 'S') FCFG.maxForce += 0.01;

  // FLOCK tweaks
  if (key === 'Z') BCFG.perception = max(20, BCFG.perception-5);
  if (key === 'X') BCFG.perception = min(200, BCFG.perception+5);
  if (key === 'E') BCFG.maxForce = max(0.01, BCFG.maxForce-0.01);
  if (key === 'R') BCFG.maxForce += 0.01;
  if (key === 'D') BCFG.maxSpeed = max(0.6, BCFG.maxSpeed-0.2);
  if (key === 'F') BCFG.maxSpeed += 0.2;

  // AUDIO FX (sin _amp.getLevel)
  if (key === '3' && delayFX) { FX.delayOn = !FX.delayOn; delayFX.amp(FX.delayOn ? 0.35 : 0.0, 0.05); }
  if (key === '4' && reverbFX){ FX.revOn   = !FX.revOn;   reverbFX.amp(FX.revOn ? 0.25 : 0.0, 0.05); }
  if (key === '5' && distFX)  { FX.distOn  = !FX.distOn;  distFX.amp(FX.distOn ? 0.22 : 0.0, 0.05); }

  // RATE
  if (key === '-') { playRate = max(0.5, playRate - 0.05); if (song) song.rate(playRate); }
  if (key === '=') { playRate = min(1.8, playRate + 0.05); if (song) song.rate(playRate); }
  if (key === '0') { playRate = 1.0; if (song) song.rate(playRate); }

  // Band-pass Q y volumen canal bajo
  if (key === '[') { bassQ = max(0.5, bassQ-0.3); bassFilter && bassFilter.res(bassQ); }
  if (key === ']') { bassQ = min(12.0, bassQ+0.3); bassFilter && bassFilter.res(bassQ); }
  if (key === ',') { bassBase = max(0.0, bassBase-0.03); bassGain && bassGain.amp(bassBase); }
  if (key === '.') { bassBase = min(0.6, bassBase+0.03); bassGain && bassGain.amp(bassBase); }

  // LOOP slicing
  if (key === 'O' || key === 'o') { loopMode = !loopMode; if (!loopMode) loopActive=false; }
  if (loopMode && '123456789'.includes(key) && song) {
    const k = int(key), dur = song.duration(), slice = 0.1;
    loopStart = dur * (k-1)*slice; loopEnd = loopStart + dur*slice;
    song.jump(loopStart); loopActive = true;
  }
  if (loopMode && (key === 'P' || key === 'p')) loopActive = false;

  // Macros manuales
  if (key === 'K' || key === 'k') spawnShockwave(mouseX, mouseY, 32);
  if (key === 'L' || key === 'l') spawnRibbonBurst(mouseX, mouseY, 42);

  // “Bajo teclado”
  const kl = key.toLowerCase();
  if (KEY_FREQ[kl]) triggerBassKey(KEY_FREQ[kl]);
  if (keyCode === SHIFT) stutterOn = true;

  // Cualquier letra no-reservada -> destellos aleatorios
  const reserved = 'KkLlTtHhSsOo';
  if (/[a-zA-Z]/.test(key) && !KEY_FREQ[kl] && !reserved.includes(key)) {
    const n = floor(random(4,10));
    for (let i = 0; i < n; i++) spawnShockwave(random(width), random(height), random(18,36));
  }
}
function keyReleased(){ if (keyCode === SHIFT) stutterOn = false; }

/* -------------------- HUD --------------------- */
function drawHUD(bBass, bMid, bHigh, level){
  noStroke(); fill(255); textSize(12);
  const l1=`Mode:${mode}  BassSculpt:${bassSculpt?'ON':'OFF'}  Img:${imageInfluence?'ON':'OFF'}  Edges:${edgeEmittersOn?'ON':'OFF'}  LoopMode:${loopMode?'ON':'OFF'}${loopActive?' (active)':''}`;
  const l2=`Flow: cellSize:${FCFG.cellSize} agents:${agents.length} maxSpeed:${FCFG.maxSpeed.toFixed(2)} maxForce:${FCFG.maxForce.toFixed(2)}`;
  const l3=`Flock: perc:${BCFG.perception} maxSpeed:${BCFG.maxSpeed.toFixed(2)} maxForce:${BCFG.maxForce.toFixed(2)} W(S,A,C)=(${BCFG.W.sep},${BCFG.W.ali},${BCFG.W.coh})`;
  const l4=`Rate:${playRate.toFixed(2)}  BassHz:${Math.round(bassCenterHz)} Q=${bassQ.toFixed(1)}  BassVol:${bassBase.toFixed(2)}  Bands[B/M/T]: ${Math.round(bBass*100)} / ${Math.round(bMid*100)} / ${Math.round(bHigh*100)}%  Level:${level.toFixed(2)}`;
  text(l1, 10, 20); text(l2, 10, 38); text(l3, 10, 56); text(l4, 10, 74);
}
```

# Calificacion rubrica 


Investigación y Experimentación — 5.0 • Conecté portada→flow/steering y FFT (bajos/medios/agudos)→S/A/C; probé Flow vs Flock variando cellSize, maxSpeed/maxForce, perception, bassQ, rate.
Intención y Diseño — 5.0 • Concepto coherente con Infinite Azure; portada define paleta/bordes; el teclado como “bajo” guía gestos visuales musicales.
Aplicación Técnica — 5.0 • FlowField y Boids sólidos; macros (shockwave, ribbons) en buffer; audio-reactivo con FFT+PeakDetect, band-pass, FX, rate/loop; sin APIs privadas.
Calidad de la Obra Final — 5.0 • Estable y responsivo; visuales densos pero legibles; entrega completa (código, link, capturas).

Nota final propuesta: 5.0
