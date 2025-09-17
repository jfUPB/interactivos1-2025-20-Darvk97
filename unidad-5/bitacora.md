
# Evidencias de la unidad 5

# Actividad 1

R//: 1. El micro:bit y el sketch de p5.js se comunican mediante el puerto serial utilizando UART a una velocidad de 115200 baudios. El micro:bit envía datos al puerto USB, y desde el navegador web, p5.js puede leer estos datos gracias a la librería p5.webserial. Esta conexión permite que el sketch reciba información directamente desde el micro:bit sin necesidad de software adicional, usando solo una página web.

El micro:bit envía una línea de texto cada 100 milisegundos. Esa línea contiene cuatro datos: el valor del acelerómetro en el eje X, el valor del acelerómetro en el eje Y, el estado del botón A y el estado del botón B. Los estados de los botones se envían como True o False, y todos los valores están separados por comas. Esta estructura permite que p5.js los lea fácilmente y los convierta en información útil para el dibujo interactivo.

2. La estructura del protocolo usado es ASCII en formato CSV (valores separados por comas), y termina con un salto de línea \n. Por ejemplo, una línea enviada puede verse así: -120,230,True,False\n. Esta estructura es simple pero efectiva, ya que permite dividir la cadena de texto fácilmente en p5.js usando la función split(","), para después convertir los valores a números o booleanos según sea necesario.

3. En el código de p5.js, los datos se leen dentro del bloque draw(), usando port.readUntil("\n") para obtener una línea completa enviada desde el micro:bit. Luego, esa línea se divide usando split(",") y se procesan los cuatro valores. Los valores del acelerómetro se ajustan para convertirlos en coordenadas de pantalla, sumando la mitad del ancho o alto de la ventana. permitiendo que el dibujo se realice desde el centro de la pantalla hacia los lados.

4. Los eventos “A pressed” y “B released” se detectan en la función updateButtonStates(). Cuando el estado del botón A pasa de false a true, se interpreta como que fue presionado, y el programa recuerda la posición del cursor para empezar a dibujar desde ahí. Cuando el botón B pasa de true a false, se interpreta como que fue soltado, y se genera un nuevo color aleatorio para los dibujos.
##capturas:

# Actividad 2

R//: 1. La imagen muestra que el micro:bit está enviando datos correctamente a través del puerto serial y la aplicación los recibe en tiempo real. Cada línea contiene valores del acelerómetro y el estado de los botones A y B, separados por comas. Esto confirma que el código en MicroPython funciona bien y que la conexión está activa.
<img width="1920" height="1032" alt="Aplicación de Conexión Serial - Google Chrome 17_09_2025 2_31_22 p  m" src="https://github.com/user-attachments/assets/24d9415d-d272-4208-83bd-95642e6e1407" />

2. Se relaciona en que esa linea empaqueta los datos en formato binario antes de enviarlos. A diferencia del formato de texto (CSV), aquí se envían los valores numéricos como bytes compactos. Por eso, en la aplicación de conexión serial, los datos aparecen como secuencias de números en hexadecimal, que representan directamente los valores del acelerómetro y botones codificados de forma eficiente.
<img width="1920" height="1032" alt="Editing interactivos1-2025-20-Darvk97_unidad-5_bitacora md at unidad5_apply · jfUPB_interactivos1-2025-20-Darvk97 - Google Chrome 17_09_2025 2_36_19 p  m" src="https://github.com/user-attachments/assets/7f3fd1c6-43a3-422a-a7d4-1887a9ab791d" />

3.¿Qué ventajas y desventajas ves en usar un formato binario en lugar de texto en ASCII?
R//: la ventaja es que los datos ocupan menos espacio y se transmiten más rápido, ya que cada valor se envía en su forma más compacta (por ejemplo, dos bytes por número entero). Esto es útil cuando se requiere eficiencia o se envían muchos datos. Sin embargo, una desventaja es que los datos no son legibles directamente, por lo que se necesita un programa específico que sepa cómo decodificarlos. En cambio, el formato ASCII es más fácil de leer y depurar, pero ocupa más espacio y puede ser más lento para transmitir.

4. En el código mostrado, se usa la línea data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState)), lo que significa que se están enviando 6 bytes por cada mensaje. El formato '>2h2B' indica lo siguiente:

- > : orden de bytes big-endian (de mayor a menor).

- 2h: dos valores tipo short (2 bytes cada uno) para xValue y yValue.

- 2B: dos valores tipo unsigned byte (1 byte cada uno) para los estados de los botones A y B.

Entonces, cada mensaje tiene:

- 2 bytes para xValue

- 2 bytes para yValue

- 1 byte para aState

- 1 byte para bState

- Total: 6 bytes por línea.
Cada byte en hexadecimal en la imagen representa parte de estos valores, enviados en secuencia para ser interpretados en el receptor.
  <img width="1920" height="1032" alt="Editing interactivos1-2025-20-Darvk97_unidad-5_bitacora md at unidad5_apply · jfUPB_interactivos1-2025-20-Darvk97 - Google Chrome 17_09_2025 2_48_36 p  m" src="https://github.com/user-attachments/assets/2480ef61-12fb-4d47-b9fa-9c90b3a98a5b" />

5. Sí, en el formato '>2h2B' es posible enviar números positivos y negativos para xValue y yValue porque el tipo h representa un entero con signo (signed short) de 2 bytes. Esto permite valores desde -32,768 hasta 32,767. En formato hexadecimal, los números positivos se ven como bytes normales (por ejemplo, 00 64 para +100), mientras que los negativos usan complemento a dos, lo que significa que, por ejemplo, -1 se representa como FF FF, y -100 como FF 9C. El receptor debe saber que está leyendo un número con signo para poder interpretarlo correctamente.

6. En este código, se están enviando los mismos datos dos veces: primero en formato binario usando struct.pack(), y luego en formato ASCII como texto legible con format(). La principal diferencia es que el binario ocupa menos bytes (6 bytes por mensaje), es más rápido de enviar y procesar, pero no es legible a simple vista. En cambio, el formato ASCII es fácil de leer y depurar, ya que muestra los números claramente separados por comas, pero ocupa más espacio (más bytes por cada mensaje) y puede ser más lento en transmisión. El binario es mejor para aplicaciones que requieren velocidad y eficiencia, mientras que el ASCII es útil para pruebas, monitoreo o debugging.

# Actividad 3

