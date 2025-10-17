
# Evidencias de la unidad 6

# Actividad 2 #

1. Piensa en cómo te conectas a Internet en casa o en la Universidad. ¿Usas Wi-Fi? ¿Un cable de red? Eso es simplemente tu “rampa de acceso” a la gran red de carreteras. ¿Qué pasaría si esa rampa se corta? Anota tus ideas.
R//: Si se corta la rampa de acceso en este caso Wi-Fi, no habría conexión con el Internet. Sería como estar en una carretera sin salida, osea el navegador no podría comunicarse con ningún servidor.


2. ¿Puedes identificar otros ejemplos de relaciones Cliente-Servidor en tu vida diaria (no necesariamente digitales)? Por ejemplo, al pedir comida en un restaurante. ¿Quién es el cliente y quién el servidor? ¿Qué se pide y qué se entrega?
R//: Al comprar boletos para un concierto, el cliente pide el tipo de entrada y la cantidad y el vendedor (el servidor) la(s) entrega. El cliente solicita, el servidor responde.


3. Toma la URL de tu sitio web favorito. Intenta identificar el protocolo, el nombre de dominio y la ruta (si la hay). ¿Qué crees que pasa si solo escribes el nombre de dominio (ej. www.google.com) sin una ruta específica? ¿Qué “página por defecto” crees que te envía el servidor?
R//: https://www.youtube.com/watch?v=2UW5xAHCs4Q&pp=ygURcGVlbGluZ2ZsZXNoIGxpdmU%3D este es un concierto de una de mis bandas favoritas y la usare como ejemplo
- Protocolo: https
- Dominio: www.youtube.com
- Ruta: /watch?v=2UW5xAHCs4Q&pp=ygURcGVlbGluZ2ZsZXNoIGxpdmU%3D
si solo se pone el dominio se ingresa a la pagina por defecto de youtube


4. Compara HTTP con los protocolos seriales que usaste.

- ¿Qué similitudes encuentras?
R//: Ambos usan protocolos para definir cómo se envían y reciben datos.

- ¿Qué diferencias clave ves?
R//: HTTP maneja textos estructurados, encabezados, tipos de contenido y códigos de estado.

- ¿Por qué crees que HTTP necesita ser más complejo que un simple envío de bytes como hacías con el micro:bit?
R//: HTTP es más complejo porque debe servir millones de usuarios, archivos y formatos distintos.


5. Piensa en una página web simple, como un formulario de login.

- ¿Qué parte crees que es HTML (ej. los campos de texto, el botón)?
R//: HTML: estructura (campos de usuario, contraseña, botón).
  
- ¿Qué parte es CSS (ej. el color del botón, el tipo de letra)?
R//: estilos visuales (colores, tamaño, fuentes).
  
- ¿Qué parte es JavaScript (ej. la comprobación de si escribiste algo antes de enviar, el mensaje de “contraseña incorrecta” que aparece sin recargar la página)?
  R//: interacción (validar si los campos están vacíos, mostrar mensaje de error sin recargar la página).


6. Compara el bucle draw() de p5.js con este modelo de “esperar a que algo pase y reaccionar”.

- ¿Qué ventajas crees que tiene el modelo basado en eventos para una interfaz de usuario web?
R//: Es más eficiente, porque el código solo se ejecuta cuando ocurre algo (clic, mensaje, conexión).

- ¿Sería eficiente tener un bucle draw() redibujando toda la página 60 veces por segundo si nada ha cambiado?
R//: No necesita redibujar la página 60 veces por segundo como p5.js, ahorrando recursos.


7. ¿Por qué crees que podría ser útil usar JavaScript tanto en el cliente (navegador) como en el servidor? ¿Se te ocurre alguna ventaja para los desarrolladores?
R//: El mismo lenguaje en ambos lados simplifica el desarrollo, permite compartir lógica y datos fácilmente, y reduce la curva de aprendizaje.


8. Resume con tus propias palabras la diferencia fundamental entre una comunicación HTTP tradicional y una comunicación usando WebSockets/Socket.IO. ¿En qué tipo de aplicaciones has visto o podrías imaginar que se usa esta comunicación en tiempo real?
R//: HTTP es comunicación “pregunta-respuesta”, WebSocket es una conexión continua donde ambos pueden enviarse mensajes en cualquier momento y 
se usa en chats, videojuegos en línea, etc.


# Actividad 3 #

4. Intenta acceder a http://localhost:3000/page1. ¿Funciona?
R//: http://localhost:3000/page1 = No funciona

5. Ahora intenta acceder a http://localhost:3000/pagina_uno. ¿Funciona?
R//: http://localhost:3000/pagina_uno = Funciona

6. ¿Qué te dice esto sobre cómo el servidor asocia URLs con respuestas? Restaura el código.
R//: las rutas URLs están definidas explícitamente en el servidor, y deben coincidir exactamente.

7. Abre http://localhost:3000/page1 en una pestaña. Observa la terminal del servidor. ¿Qué mensaje ves? Anota el ID.

Abre http://localhost:3000/page2 en OTRA pestaña. Observa la terminal. ¿Qué mensaje ves? ¿El ID es diferente?

Cierra la pestaña de page1. Observa la terminal. ¿Qué mensaje ves? ¿Coincide el ID con el que anotaste?

Cierra la pestaña de page2. Observa la terminal.
R//: Cada pestaña abierta crea un nuevo socket con su propio ID. Al cerrarla, la terminal muestra:

User disconnected: <mismo ID>

8. Inicia el servidor y abre page1 y page2.

Mueve la ventana de page1. Observa la terminal del servidor. ¿Qué evento se registra (win1update o win2update)? ¿Qué datos (Data:) ves?

Mueve la ventana de page2. Observa la terminal. ¿Qué evento se registra ahora? ¿Qué datos ves?

Experimento clave: cambia socket.broadcast.emit(‘getdata’, page1); por socket.emit(‘getdata’, page1); (quitando broadcast). Reinicia el servidor, abre ambas páginas. Mueve page1. ¿Se actualiza la visualización en page2? ¿Por qué sí o por qué no? (Pista: ¿A quién le envía el mensaje socket.emit?). Restaura el código a broadcast.emit.

R//: - Cuando muevo page1 aparece win1update y cuando muevo page2, win2update.
- Los datos son vectores de posición.
- Al usar socket.emit, los mensajes solo regresan al mismo cliente, por eso page2 ya no se actualiza, broadcast.emit envía el mensaje a todos los demás clientes excepto al emisor, en resumen broadcast es lo que permite la comunicación entre páginas.

9. Detén el servidor.

Cambia const port = 3000; a const port = 3001;.

Inicia el servidor. ¿Qué mensaje ves en la consola? ¿En qué puerto dice que está escuchando?

Intenta abrir http://localhost:3000/page1. ¿Funciona?

Intenta abrir http://localhost:3001/page1. ¿Funciona?

¿Qué aprendiste sobre la variable port y la función listen? Restaura el puerto a 3000.

R//: - El servidor muestra “Server listening on port 3001”.
- Al entrar en localhost:3000 no carga nada, pero sí funciona en localhost:3001.
- La variable port define en qué “puerta” escucha el servidor.


# Actividad 4 #

1. Abre page2.html en tu navegador (con el servidor corriendo).

Abre la consola de desarrollador (F12).

Detén el servidor Node.js (Ctrl+C).

Refresca la página page2.html. Observa la consola del navegador. ¿Ves algún error relacionado con la conexión? ¿Qué indica?

Vuelve a iniciar el servidor y refresca la página. ¿Desaparecen los errores?
R//: En la consola del navegador aparece:

WebSocket connection failed: Error in connection establishment


2. Comenta la línea socket.emit(‘win2update’, currentPageData, socket.id); dentro del listener connect.

Reinicia el servidor y refresca page1.html y page2.html.

Mueve la ventana de page2 un poco para que envíe una actualización.

¿Qué pasó? ¿Por qué? 
R//: Ningún dato inicial se envía al servidor, por tanto, page2 no recibe actualizaciones, la comunicación comienza cuando el cliente se conecta y envía su estado.

3. Abre ambas páginas (es posible que ya las tengas abiertas).

Mueve la ventana de page1. Observa la consola del navegador de page2. ¿Qué datos muestra?

Mueve la ventana de page2. Observa la consola de page1. ¿Qué pasa? ¿Por qué?
R//: Cuando muevo page1, la consola de page2 muestra los datos recibidos, y viceversa, esto confirma que la sincronización es bidireccional.

4. Observa checkWindowPosition() en page2.js y modifica el código del if para comprobar si el código dentreo de este se ejecuta.

Mueve cada ventana y observa las consolas.

¿Qué puedes concluir y por qué?
R//: Al modificar el if, se puede comprobar que solo ejecuta el código cuando la ventana realmente cambia de posición, esto evita enviar datos innecesarios al servidor.


5. Cambia el background(220) para que dependa de la distancia entre las ventanas. Puedes calcular la magnitud del resultingVector usando let distancia = resultingVector.mag(); y luego usa map() para convertir esa distancia a un valor de gris o color. background(map(distancia, 0, 1000, 255, 0)); (ajusta el rango 0-1000 según sea necesario).

Inventa otra modificación creativa.
R//: 

let distancia = resultingVector.mag();
background(map(distancia, 0, 1000, 255, 0));

A medida que las ventanas están más cerca, el fondo se aclara, representa visualmente la cercanía entre los usuarios.


# Actividad 5 #

es una aplicación interactiva llamada "dualvinci", donde dos usuarios dibujan simultáneamente sobre un mismo lienzo virtual.
Cuando un usuario dibuja en su ventana (page1 o page2), el trazo aparece en tiempo real en la otra.

## server.js ##

// =============================
// SERVIDOR NODE + SOCKET.IO
// =============================
const express = require("express");
const app = express();
const http = require("http").createServer(app);
const io = require("socket.io")(http);
const port = 3000;

app.use(express.static("public"));

app.get("/page1", (req, res) => {
  res.sendFile(__dirname + "/public/page1.html");
});
app.get("/page2", (req, res) => {
  res.sendFile(__dirname + "/public/page2.html");
});

io.on("connection", (socket) => {
  console.log("Cliente conectado:", socket.id);

  socket.on("draw", (data) => {
    console.log("Dibujo recibido:", data);
    socket.broadcast.emit("draw", data);
  });

  socket.on("disconnect", () => {
    console.log("Cliente desconectado:", socket.id);
  });
});

http.listen(port, () => {
  console.log(`Servidor escuchando en http://localhost:${port}`);
});


## public/page1.html ##

<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Dibujo Compartido - Usuario 1</title>
  <script src="/socket.io/socket.io.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
  <script src="page1.js"></script>
</head>
<body></body>
</html>


## public/page2.html ##

<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Dibujo Compartido - Usuario 2</title>
  <script src="/socket.io/socket.io.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
  <script src="page2.js"></script>
</head>
<body></body>
</html>


## public/page1.js ##

let socket;
let drawing = false;

function setup() {
  createCanvas(600, 400);
  background(255);
  socket = io.connect();
  socket.on("draw", drawFromOtherUser);
}

function mouseDragged() {
  drawing = true;
  stroke(0, 100, 255);
  strokeWeight(4);
  line(pmouseX, pmouseY, mouseX, mouseY);
  socket.emit("draw", { x1: pmouseX, y1: pmouseY, x2: mouseX, y2: mouseY, color: [0,100,255] });
}

function drawFromOtherUser(data) {
  stroke(data.color);
  strokeWeight(4);
  line(data.x1, data.y1, data.x2, data.y2);
}


## public/page2.js ##

let socket;

function setup() {
  createCanvas(600, 400);
  background(255);
  socket = io.connect();
  socket.on("draw", drawFromOtherUser);
}

function mouseDragged() {
  stroke(255, 0, 100);
  strokeWeight(4);
  line(pmouseX, pmouseY, mouseX, mouseY);
  socket.emit("draw", { x1: pmouseX, y1: pmouseY, x2: mouseX, y2: mouseY, color: [255,0,100] });
}

function drawFromOtherUser(data) {
  stroke(data.color);
  strokeWeight(4);
  line(data.x1, data.y1, data.x2, data.y2);
}



- Idea: crear una experiencia colaborativa en la que dos personas dibujan en un mismo lienzo compartido en tiempo real.
- Servidor: maneja eventos de conexión, dibujo y desconexión.
- Clientes: envían coordenadas de líneas y las reciben del otro.
- Resultado: ambos usuarios pueden ver y complementar el dibujo del otro sin retraso perceptible.
- Aprendizaje: Socket.IO simplifica la sincronización en tiempo real. Se entiende cómo un evento en un cliente puede reflejarse instantáneamente en otro.


