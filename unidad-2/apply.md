# Unidad 2


## 🛠 Fase: Apply

Concepto: Ríos de Ruido — enjambre de partículas que deja estelas mientras recorre un campo vectorial en tiempo real. El flujo reacciona a tu mouse y tu voz: el sonido aumenta la velocidad, el clic crea pozos de gravedad. Es un paisaje invisible revelado por el movimiento.

Motion 101: cada partícula tiene posición, velocidad y aceleración; en cada frame: acc se calcula (no se acumula), vel.add(acc) y pos.add(vel) con límite de velocidad y leve rozamiento.

Algoritmo de aceleración: curl noise 2D (rotación del gradiente de Perlin noise), que genera flujos en remolino sin fuentes ni sumideros, ideal para patrones fluidos. Se modula en tiempo real: mouseX escala del campo, mouseY intensidad, micrófono amplifica la fuerza, clic añade gravedad local.

Interactividad: mouse para escala/intensidad, clic para gravedad, m activa/desactiva mic, c limpia pantalla, h HUD, s guarda captura.

```js

let movers = [];
const NUM = 1800;        // # de partículas
let mic, useMic = true;
let showHUD = true;

function setup() {
  createCanvas(windowWidth, windowHeight);
  pixelDensity(1);
  background(5);
  noiseDetail(4, 0.5);

  // Inicializar partículas
  for (let i = 0; i < NUM; i++) {
    movers.push(new Mover(createVector(random(width), random(height))));
  }

  // Micrófono
  mic = new p5.AudioIn();
  userStartAudio().catch(()=>{ /* se activa luego */ });
}

function draw() {

  noStroke();
  fill(0, 12); 
  rect(0, 0, width, height);

  const scale = map(constrain(mouseX, 0, width), 0, width, 0.002, 0.02); // frecuencia del campo
  const strength = map(height - constrain(mouseY, 0, height), 0, height, 0.2, 2.4); // intensidad
  const t = millis() * 0.00015;
  const micLevel = useMic ? mic.getLevel() : 0;
  const audioBoost = 1 + map(micLevel, 0, 0.2, 0, 2, true);

  // Dibujar/actualizar partículas
  stroke(255, 220);
  strokeWeight(1);
  for (let m of movers) {
    // 1) Aceleración por curl noise
    const curl = curlNoise(m.pos.x * scale, m.pos.y * scale, t);
    let acc = curl.mult(strength * audioBoost);

    
    if (mouseIsPressed) {
      const mouse = createVector(mouseX, mouseY);
      let toMouse = p5.Vector.sub(mouse, m.pos);
      const d2 = max(36, toMouse.magSq());       
      toMouse.setMag(2200 / d2);                 
      acc.add(toMouse);
    }

    // Integración Motion 101
    m.applyAcceleration(acc);
    m.update();
    m.wrapEdges();
    m.display();
  }

  if (showHUD) drawHUD(scale, strength, audioBoost, micLevel);
}

class Mover {
  constructor(pos) {
    this.pos = pos.copy();
    this.vel = p5.Vector.random2D().mult(random(0.2));
    this.acc = createVector(0, 0);
    this.prev = pos.copy();
  }
  applyAcceleration(a) {
    this.acc.set(a); // recalculada cada frame (no acumulamos)
  }
  update() {
    this.vel.add(this.acc);
    this.vel.limit(3.5);
    this.vel.mult(0.995); // rozamiento leve
    this.prev.set(this.pos);
    this.pos.add(this.vel);
  }
  wrapEdges() {
    // Teletransporte en bordes (topología toro)
    if (this.pos.x < 0) { this.pos.x = width;  this.prev.x = this.pos.x; }
    if (this.pos.x > width) { this.pos.x = 0;  this.prev.x = this.pos.x; }
    if (this.pos.y < 0) { this.pos.y = height; this.prev.y = this.pos.y; }
    if (this.pos.y > height) { this.pos.y = 0; this.prev.y = this.pos.y; }
  }
  display() {
    line(this.prev.x, this.prev.y, this.pos.x, this.pos.y);
  }
}


function curlNoise(x, y, t) {
  const e = 0.0005;
  const n1 = noise(x + e, y, t);
  const n2 = noise(x - e, y, t);
  const dx = (n1 - n2) / (2 * e);

  const n3 = noise(x, y + e, t);
  const n4 = noise(x, y - e, t);
  const dy = (n3 - n4) / (2 * e);

  // Gradiente = (dx, dy). Curl 2D ~ rotación 90°: ( -dy, dx )
  return createVector(-dy, dx);
}

function drawHUD(scale, strength, audioBoost, micLevel) {
  noStroke();
  fill(0, 140);
  rect(12, 12, 260, 92, 10);
  fill(255);
  textSize(12);
  textLeading(16);
  text(
    `RÍOS DE RUIDO — Curl Noise
X escala: ${nf(scale,1,4)} | Y intensidad: ${strength.toFixed(2)}
Mic: ${useMic ? "ON" : "OFF"} (nivel: ${micLevel.toFixed(3)}, boost: ${audioBoost.toFixed(2)})
Controles: m mic · c limpiar · s guardar · h HUD · click = gravedad`,
    22, 34
  );
}

function keyPressed() {
  if (key === 'c' || key === 'C') {
    background(5);
  } else if (key === 's' || key === 'S') {
    saveCanvas('rios_de_ruido', 'png'); // ← captura (screenshot)
  } else if (key === 'h' || key === 'H') {
    showHUD = !showHUD;
  } else if (key === 'm' || key === 'M') {
    useMic = !useMic;
    if (useMic) {
      userStartAudio().then(() => mic.start()).catch(()=>{ /* esperar otro gesto */ });
    } else {
      mic.stop();
    }
  }
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}
```
<img width="1266" height="1220" alt="rios_de_ruido" src="https://github.com/user-attachments/assets/18b54dab-96c0-40cf-8521-5eaf4096b83c" />




