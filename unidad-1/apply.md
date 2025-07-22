# Unidad 1

## 🛠 Fase: Apply

### Actividad 8
quiero hacer y trabajar un concepto que me encanta que seria a raiz del elctrocardiograma que sigue un movimirnto similar al prelin noise, entonces me encantaria representar como cada latido da paso a la creacion de una imagen llena de vida, porque cada latido representa eso ( tendre que representar a alguien con taquicardia), de igual forma aqui podriamos usar una distribucion gausiana para guardar los latidos cotrolados para generar una linea distinta (normal) y un random walker para cuando hay irregularidades

Primero genere una para cuando es nirmal y regular, con los siguientes parametros 

```js
let x = 0;
let y;
let baseLine = 200;
let amplitude = 50;
let freq = 0.02;

let randomWalkY;

function setup() {
  createCanvas(800, 400);
  background(0);
  y = baseLine;
  randomWalkY = baseLine;
  strokeWeight(2);
  frameRate(60);
}

function draw() {
let noiseY = baseLine + amplitude * sin(TWO_PI * freq * x) + randomGaussian() * 5;
    stroke(0, 255, 100, 150);
    line(x, y, x + 1, noiseY);
}

```

Que nos da la siguiente imagen 

<img width="796" height="399" alt="image" src="https://github.com/user-attachments/assets/7e934b6d-d5e1-4897-be53-ab496e372323" />
