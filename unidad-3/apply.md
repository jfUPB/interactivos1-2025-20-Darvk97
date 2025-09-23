# Unidad 3


## 🛠 Fase: Apply

## Actividad 6

index.html

    <!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="UTF-8">
    <title>Bomba 2.0 en p5.js</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
    <script src="sketch.js"></script>
    </head>
    <body>
    </body>
    </html>


sketch.js

    let state = "CONFIG";
    let count = 20;
    let startTime;
    let password = ["A", "B", "A"];
    let keyInput = [];
    let keyIndex = 0;

    function setup() {
    createCanvas(400, 400);
    textAlign(CENTER, CENTER);
    textSize(32);
    startTime = millis();
    }

    function draw() {
    background(220);

    if (state === "CONFIG") {
    fill(0);
    text("CONFIG", width / 2, 50);
    text("Tiempo: " + count, width / 2, height / 2);
    text("W ↑ | S ↓ | SPACE = ARM", width / 2, height - 50);

    } else if (state === "ARMED") {
    fill(255, 0, 0);
    text("ARMED", width / 2, 50);
    text("Tiempo: " + count, width / 2, height / 2);

    if (millis() - startTime > 1000) {
      startTime = millis();
      count--;
      if (count <= 0) {
        state = "EXPLODED";
      }
    }

    } else if (state === "EXPLODED") {
    fill(0);
    text(" BOOM ", width / 2, height / 2);
    text("Presiona T para reiniciar", width / 2, height - 50);
      }
    }

    function keyPressed() {
    if (state === "CONFIG") {
    if (key === "W") {
      count = min(count + 1, 60);
    } else if (key === "S") {
      count = max(count - 1, 10);
    } else if (key === " ") { // SPACE = shake
      state = "ARMED";
      startTime = millis();
    }
    }

    else if (state === "ARMED") {
    if (key === "A") {
      keyInput[keyIndex] = "A";
      keyIndex++;
    } else if (key === "D") {
      keyInput[keyIndex] = "B";
      keyIndex++;
    } else if (key === " ") {
      // Mejora: reducir tiempo a la mitad con shake
      count = Math.ceil(count / 2);
    }

    if (keyIndex === password.length) {
      let passIsOK = true;
      for (let i = 0; i < password.length; i++) {
        if (keyInput[i] !== password[i]) {
          passIsOK = false;
          break;
        }
      }

      if (passIsOK) {
        count = 20;
        keyIndex = 0;
        keyInput = [];
        state = "CONFIG";
      } else {
        keyIndex = 0;
        keyInput = [];
      }
    }
    }

    else if (state === "EXPLODED") {
    if (key === "T") {
      count = 20;
      keyIndex = 0;
      keyInput = [];
      state = "CONFIG";
      startTime = millis();
    }
    }
    }

 ## Actividad 7

index.html

    <!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="UTF-8">
    <title>Bomba 2.0 en p5.js + micro:bit</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/p5.serialport@1.1.2/lib/p5.serialport.min.js"></script>
    <script src="sketch.js"></script>
     </head>
    <body>
    </body>
    </html>


sketch.js

    let state = "CONFIG";
    let count = 20;
    let startTime;
    let password = ["A", "B", "A"];
    let keyInput = [];
    let keyIndex = 0;

    // Serial
    let port;
    let connectBtn;
    let connectionInitialized = false;
    let validChars = "WASD ST"; // W,A,S,D, SPACE, T

    function setup() {
    createCanvas(400, 400);
    textAlign(CENTER, CENTER);
    textSize(32);
    startTime = millis();

    // Inicializar puerto serie
    port = createSerial();
    connectBtn = createButton("Conectar micro:bit");
    connectBtn.position(20, height - 40);
    connectBtn.mousePressed(connectBtnClick);
    }

    function draw() {
      background(220);

    // Inicializar conexión si se abre
    if (port.opened() && !connectionInitialized) {
    port.clear();
    connectionInitialized = true;
    }

    // Leer datos desde micro:bit
    if (port.availableBytes() > 0) {
    let data = port.read(1);
    if (data) handleInput(data);
    }

    // Render estados
    if (state === "CONFIG") {
    fill(0);
    text("CONFIG", width / 2, 50);
    text("Tiempo: " + count, width / 2, height / 2);
    text("W ↑ | S ↓ | SPACE = ARM", width / 2, height - 100);

    } else if (state === "ARMED") {
    fill(255, 0, 0);
    text("ARMED", width / 2, 50);
    text("Tiempo: " + count, width / 2, height / 2);

    if (millis() - startTime > 1000) {
      startTime = millis();
      count--;
      if (count <= 0) {
        state = "EXPLODED";
      }
    }

    } else if (state === "EXPLODED") {
    fill(0);
    text(" BOOM ", width / 2, height / 2);
    text("Presiona T para reiniciar", width / 2, height - 100);
    }

    // Botón de conexión
     if (!port.opened()) {
    connectBtn.html("Conectar micro:bit");
    } else {
    connectBtn.html("Desconectar");
    }
    }

    function keyPressed() {
    let k = key.toUpperCase();
    if (k === " ") k = " "; // Espacio

    if (validChars.includes(k)) {
    handleInput(k);
    port.write(k); // opcional: enviar a micro:bit también
    }
    }

    function handleInput(k) {
    if (state === "CONFIG") {
    if (k === "W") {
      count = min(count + 1, 60);
    } else if (k === "S") {
      count = max(count - 1, 10);
    } else if (k === " ") {
      state = "ARMED";
      startTime = millis();
    }
    }

    else if (state === "ARMED") {
    if (k === "A") {
      keyInput[keyIndex] = "A";
      keyIndex++;
    } else if (k === "D") {
      keyInput[keyIndex] = "B";
      keyIndex++;
    } else if (k === " ") {
      count = Math.ceil(count / 2); // shake reduce tiempo a la mitad
    }

    if (keyIndex === password.length) {
      let passIsOK = true;
      for (let i = 0; i < password.length; i++) {
        if (keyInput[i] !== password[i]) {
          passIsOK = false;
          break;
        }
      }

      if (passIsOK) {
        count = 20;
        keyIndex = 0;
        keyInput = [];
        state = "CONFIG";
      } else {
        keyIndex = 0;
        keyInput = [];
      }
    }
    }

    else if (state === "EXPLODED") {
    if (k === "T") {
      count = 20;
      keyIndex = 0;
      keyInput = [];
      state = "CONFIG";
      startTime = millis();
    }
    }
    }

    function connectBtnClick() {
    if (!port.opened()) {
    port.open("MicroPython", 115200);
    connectionInitialized = false;
    } else {
    port.close();
    }
    }


Código micro:bit

    from microbit import *
    import utime

    while True:
    if button_a.was_pressed():
        uart.write('A')
    if button_b.was_pressed():
        uart.write('B')
    if accelerometer.was_gesture('shake'):
        uart.write(' ')
    if pin_logo.is_touched():
        uart.write('T')
    utime.sleep_ms(100)
