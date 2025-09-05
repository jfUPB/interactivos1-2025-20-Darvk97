# Unidad 4


## 🛠 Fase: Apply

## Actividad 5

sketch.js:

    'use strict';

    var tileCount = 20;
    var actRandomSeed = 0;

    var circleAlpha = 130;
    var circleColor;

    let xValue = 0;
    let yValue = 0;
    let aState = false;
    let bState = false;

    let port;
    let reader;
    let decoder = new TextDecoder();
    let buffer = '';

    function setup() {
      createCanvas(600, 600);
      noFill();
      circleColor = color(0, 0, 0, circleAlpha);

      const connectButton = document.getElementById('connectButton');
      connectButton.addEventListener('click', connectMicrobit);
    }

     function draw() {
      translate(width / tileCount / 2, height / tileCount / 2);

      background(255);

      randomSeed(actRandomSeed);

     // Color según botones del micro:bit
      if (aState) {
        circleColor = color(0, 80, 80, circleAlpha);
      } else if (bState) {
        circleColor = color(220, 80, 80, circleAlpha);
      } else {
        circleColor = color(0, 0, 0, circleAlpha);
      }

      stroke(circleColor);

      // Aquí está el cambio: movimiento y tamaño controlados por micro:bit
      strokeWeight(yValue / 60);

        for (var gridY = 0; gridY < tileCount; gridY++) {
        for (var gridX = 0; gridX < tileCount; gridX++) {

        var posX = width / tileCount * gridX;
        var posY = height / tileCount * gridY;

        var shiftX = random(-xValue, xValue) / 20;
        var shiftY = random(-xValue, xValue) / 20;

        ellipse(posX + shiftX, posY + shiftY, yValue / 15, yValue / 15);
        }
      }
    }

    function mousePressed() {
    actRandomSeed = random(100000);
    }
  
    function keyReleased() {
    if (key == 's' || key == 'S') saveCanvas('P_2_1_2_01_microbit', 'png');
    }

    async function connectMicrobit() {
      try {
      port = await navigator.serial.requestPort();
      await port.open({ baudRate: 115200 });
      readLoop();
      const connectButton = document.getElementById('connectButton');
      connectButton.disabled = true;
      connectButton.innerText = 'Micro:bit conectado';
      console.log("Conectado al micro:bit");
    } catch (error) {
      console.error('Error al conectar micro:bit:', error);
      alert("No se pudo conectar al micro:bit: " + error.message);
        }
      }
  
      async function readLoop() {
    while (port.readable) {
      reader = port.readable.getReader();

      try {
        while (true) {
        const { value, done } = await reader.read();
        if (done) break;
        if (value) {
          buffer += decoder.decode(value);
          let lines = buffer.split('\n');
          buffer = lines.pop();
          for (let line of lines) {
            parseLine(line.trim());
            }
          }
        }
          } catch (error) {
            console.error(error);
          } finally {
            reader.releaseLock();
          }
        }
      }

  function parseLine(line) {
    const parts = line.split(',');
    if (parts.length === 4) {
      xValue = int(parts[0]);
      yValue = int(parts[1]);
      aState = parts[2].toLowerCase() === 'true' || parts[2] === '1';
      bState = parts[3].toLowerCase() === 'true' || parts[3] === '1';
    }
  }


index.html:

<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>P_2_1_2_01 + micro:bit</title>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.6.0/lib/p5.min.js"></script>
  <script src="sketch.js"></script>
  <style>
    body {
      margin: 0;
      background: #fff;
      overflow: hidden;
    }
    #connectButton {
      position: absolute;
      top: 10px;
      left: 10px;
      padding: 8px 12px;
      font-size: 16px;
      cursor: pointer;
      z-index: 10;
    }
  </style>
</head>
<body>
  <button id="connectButton">Conectar micro:bit</button>
</body>
</html>



