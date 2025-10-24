
# Evidencias de la unidad 7

# Actividad 1 #

1. ¿Qué URL de Dev Tunnels obtuviste? ¿Por qué crees que necesitamos usar esta URL en lugar de http://localhost:3000 o la IP local de tu computador para que el celular se conecte?
R//: La URL fue algo como https://ejemplo.use2.devtunnels.ms/. Se usa esta porque localhost solo funciona en el mismo dispositivo y la IP local solo en la misma red. Dev Tunnels crea una URL pública y segura que conecta el servidor local con Internet, permitiendo que el celular acceda desde cualquier lugar.

2. Describe brevemente qué hace npm install y npm start.
R//: - npm install descarga e instala las dependencias necesarias del proyecto (como express y socket.io).
- npm start ejecuta el servidor Node.js usando el archivo principal (server.js).

3. ¿Qué mensajes observaste en la terminal del servidor al conectar el cliente de escritorio y el cliente móvil? ¿Eran diferentes los mensajes o identificadores?
R//: En la terminal aparecieron mensajes como:

“New client connected”

“Received message = …”

“Client disconnected”

Cada cliente (móvil y escritorio) tiene un identificador distinto, lo que permite al servidor diferenciarlos.

4. Describe el comportamiento observado: ¿Funcionó la interacción? ¿Hubo algún retraso (latencia)?
R//: Sí funcionó correctamente. El círculo del escritorio seguía el movimiento del dedo en el celular. Hubo una leve latencia, pero casi imperceptible.


# Actividad 2 #

1. Explica con tus propias palabras: ¿Por qué es necesario Dev Tunnels en este escenario y cómo funciona conceptualmente?
R//: Es necesario porque el servidor local no es accesible desde Internet. Dev Tunnels crea un túnel entre una URL pública y el puerto local (3000), permitiendo que los dispositivos externos (como el celular) se conecten de forma segura al servidor local.

2. Describe la función de touchMoved() y por qué se usa la variable threshold en el cliente móvil.
R//: - touchMoved() se ejecuta cada vez que el usuario mueve el dedo en la pantalla. Envía las coordenadas actuales del toque al servidor.
- threshold sirve para evitar enviar demasiados mensajes por movimientos muy pequeños, reduciendo el tráfico y haciendo la conexión más eficiente.

3. Compara brevemente Dev Tunnels con simplemente usar la IP local. ¿Cuáles son las ventajas y desventajas de cada uno?
R//: - IP local:

ventajas: Conexión directa y rápida si ambos están en la misma red.

desventajas: Solo funciona dentro de la misma red Wi-Fi y puede ser bloqueada por firewalls.

- Dev Tunnels:

ventajas: Permite acceso desde cualquier red y ofrece conexión segura.

desventajas: Depende de Internet y puede tener un poco más de latencia.


# Actividad 3 # 

1. ¿Cuál es la función principal de express.static(‘public’) en este servidor? ¿Cómo se compara con el uso de app.get(‘/ruta’, …) del servidor de la Unidad 6?
R//: express.static('public') sirve para que el servidor entregue automáticamente los archivos de la carpeta public sin necesidad de definir rutas específicas. A diferencia de app.get('/ruta', ...), no requiere crear una función por cada archivo que se sirva.

2. Explica detalladamente el flujo de un mensaje táctil: ¿Qué evento lo envía desde el móvil? ¿Qué evento lo recibe el servidor? ¿Qué hace el servidor con él? ¿Qué evento lo envía el servidor al escritorio? ¿Por qué se usa socket.broadcast.emit en lugar de io.emit o socket.emit en este caso?
R//: 1) El móvil envía el evento touch usando socket.emit() desde touchMoved().

2) El servidor recibe este mensaje con socket.on('touch', ...).

3) Luego lo reenvía al escritorio con socket.broadcast.emit('touch', datos).

Se usa broadcast para que el mensaje se envíe a todos los demás clientes (el escritorio) excepto al emisor (el móvil).

3. Si conectaras dos computadores de escritorio y un móvil a este servidor, y movieras el dedo en el móvil, ¿Quién recibiría el mensaje retransmitido por el servidor? ¿Por qué?
R//: Los dos escritorios recibirían el mensaje, porque el servidor usa socket.broadcast.emit, que envía los datos a todos los clientes conectados menos al que los envió.

4. ¿Qué información útil te proporcionan los mensajes console.log en el servidor durante la ejecución?
R//: Muestran conexiones, desconexiones y los datos transmitidos, lo cual ayuda a verificar que el flujo de mensajes funciona correctamente y que los clientes están activos.


# Actividad 4 #

Realiza un diagrama donde muestres el flujo completo de datos y eventos entre los tres componentes: móvil, servidor y escritorio. Puedes ilustrar con un ejemplo de coordenadas táctiles (x, y) y cómo viajan a través del sistema.
R//: Móvil (p5.js - touchMoved)
         │
         │ socket.emit('touch', {x, y})
         ▼
Servidor Node.js (Socket.IO)
         │
         │ socket.broadcast.emit('touch', {x, y})
         ▼
Escritorio (p5.js - recibe coordenadas)

El usuario toca (x=120, y=300) → el móvil envía las coordenadas → el servidor las retransmite → el escritorio dibuja el círculo en esa posición.


# Actividad 5 #

# App #

## server.js ##

// ==========================
// server.js
// Sincroniza móvil ↔ escritorio
// ==========================
const express = require('express');
const http = require('http');
const socketIO = require('socket.io');
const path = require('path');

const app = express();
const server = http.createServer(app);
const io = socketIO(server);

// Servir archivos estáticos desde "public"
app.use(express.static(path.join(__dirname, 'public')));

// Rutas para desktop y móvil
app.get('/desktop', (req, res) => {
res.sendFile(path.join(__dirname, 'public/desktop.html'));
});

app.get('/mobile', (req, res) => {
res.sendFile(path.join(__dirname, 'public/mobile.html'));
});

// --- Conexión con socket.io ---
io.on('connection', (socket) => {
console.log('Nuevo cliente conectado:', socket.id);

  // Recibir datos de toque desde el móvil y reenviar al escritorio
socket.on('touch', (data) => {
    console.log('Datos recibidos del móvil:', data);
    io.emit('touch', data); // Reenvía el toque a TODOS los clientes conectados
});

socket.on('disconnect', () => {
    console.log('Cliente desconectado:', socket.id);
});
});

// Iniciar servidor
const PORT = 3000;
server.listen(PORT, () => {
console.log(`Servidor corriendo en: http://localhost:${PORT}`);
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(` Servidor escuchando en http://localhost:${PORT}`);
});

## public/mobile/Index.html ##

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Móvil - Control táctil</title>
    <script src="https://cdn.socket.io/4.7.2/socket.io.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>
    <style>
    body {
        margin: 0;
        padding: 0;
        overflow: hidden;
        background-color: #111;
        color: #fff;
        text-align: center;
        font-family: sans-serif;
}
    #info {
        position: absolute;
        width: 100%;
        top: 20px;
        font-size: 18px;
}
    </style>
    </head>
    <body>
    <div id="info">Toca la pantalla para mover el círculo </div>
    <script src="sketch.js"></script>
    </body>
</html>

## public/mobile/sketch.js ##

// ==========================
// Cliente móvil: Envío de toques al escritorio
// ==========================
let socket;
let img;

function preload() {
  // Carga una imagen de fondo opcional (puedes quitarla si no la necesitas)
  img = loadImage('WhatsApp Image 2025-10-21 at 6.57.07 PM.jpeg');
}

function setup() {
  createCanvas(windowWidth, windowHeight);
  background(0);
  textAlign(CENTER, CENTER);
  fill(255);
  textSize(22);
  text("Toca y mueve el dedo ", width / 2, height / 2);

  // Conexión con el servidor
  socket = io.connect();
}

function touchMoved() {
  // Normalizamos las coordenadas (0–1) para adaptarlas al tamaño del escritorio
  let touchData = {
    x: mouseX / width,
    y: mouseY / height
  };

  // Enviar las coordenadas al servidor
  socket.emit("touch", touchData);

  // Mostrar una pequeña estela local (efecto visual)
  noStroke();
  fill(random(200, 255), random(100, 255), random(200, 255), 150);
  ellipse(mouseX, mouseY, 25, 25);

  return false; // Previene scroll en móvil
}

## public/desktop/index.html ##

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Escritorio - Visualización</title>
    <script src="https://cdn.socket.io/4.7.2/socket.io.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/addons/p5.sound.min.js"></script>
    <style>
    
    body {
        margin: 0;
        padding: 0;
        overflow: hidden;
        background-color: #000;
        color: #fff;
        font-family: sans-serif;
}
    #info {
        position: absolute;
        top: 20px;
        left: 20px;
        font-size: 16px;
}
    </style>
</head>
<body>
    <div id="info">Círculo controlado desde el móvil </div>
    <script src="sketch.js"></script>
</body>
</html>


## public/desktop/sketch.js ##

  let spectrum = fft.analyze();
  let bass = fft.getEnergy("bass");

  // Cambia el color del tint según la música
  let brillo = map(bass, 0, 255, 50, 255);

  // Dibujar todas las posiciones recibidas del móvil
  for (let p of puntos) {
    push();
    tint(brillo, 255, 255, 200); // cambia la intensidad con el bajo
    image(gifImg, p.x - gifImg.width / 4, p.y - gifImg.height / 4, 100, 100);
    pop();
  }

  // Eliminar puntos viejos (para que desaparezcan)
  puntos = puntos.filter(p => millis() - p.tiempo < 2000);
}.y = y;
    this.r = 20;
    this.alpha = 255;
  }
  update() {
    this.r += 2;
    this.alpha -= 3;
  }
  show(hueVal) {
    fill(hueVal, 80, 100, this.alpha / 255);
    ellipse(this.x, this.y, this.r);
  }
  isDead() {
    return this.alpha <= 0;
  }
}


1. Diseña una aplicación interactiva que use el touch del móvil para controlar una visuales de tema musical de tu elección. Las visuales correrán en una aplicación de escritorio (desktop). Recuerda que ambas aplicaciones las construirás usando p5.js y utilizando el servidor Node.js como puente.
R//: Diseñé una aplicación de visuales musicales donde el movimiento del dedo en el móvil controla partículas que reaccionan al ritmo de una canción en el escritorio.

2. Implementa tu diseño. Puedes usar IA generativa para ayudarte a escribir el código, pero primero debes hacer el diseño de lo que quieres.
R//: El diseño muestra un fondo dinámico de partículas que cambian de color y ritmo según el toque del usuario. El móvil controla la dirección y velocidad de las partículas.

3. Incluye todos los códigos (servidor y clientes) en tu bitácora.
R//:

server.js → maneja la conexión con Socket.IO

mobile/sketch.js → captura y envía coordenadas táctiles

desktop/sketch.js → recibe datos y genera visualización artística

# Autoevaluacion #

Nota: 4.6

- Actividad 01:
Configuré correctamente el servidor Node.js y el túnel con Dev Tunnels, logrando la conexión entre celular y computador sin errores.

- Actividad 02:
Comprendí y expliqué claramente el funcionamiento de Dev Tunnels y los eventos táctiles en p5.js.

- Actividad 03:
Analicé el flujo de mensajes del servidor con precisión y entendí el uso de socket.broadcast.emit.

- Actividad 04:
Representé el flujo de datos entre móvil, servidor y escritorio con claridad y coherencia.

- Actividad 05:
Diseñé e implementé una aplicación interactiva funcional y creativa que conecta móvil y escritorio con éxito.

