# Unidad 2


## 🛠 Fase: Apply

## Actividad 04

Estados de la máquina

STATE_CONFIG → La bomba está en modo configuración (tiempo editable, no cuenta).

STATE_ARMED → La bomba inicia la cuenta regresiva.

STATE_EXPLODED → La bomba explota (se activa el speaker).

Eventos

- UP (botón A presionado) → Aumenta el tiempo (+1 s, máx. 60 s).

- DOWN (botón B presionado) → Disminuye el tiempo (-1 s, mín. 10 s).

- ARMED (gesto shake) → Activa la bomba y arranca la cuenta regresiva.

- Tiempo transcurrido (cada 1000 ms) → Resta un segundo al contador.

- Contador llega a 0 → Explosión.

- Touch (botón táctil presionado) → Regresa a modo configuración.

Acciones

En STATE_CONFIG:

- Mostrar en pantalla el valor actual del tiempo.

- Incrementar/decrementar el tiempo según botones.

En STATE_ARMED:

- Iniciar cuenta regresiva.

- Actualizar display con el tiempo restante.

En STATE_EXPLODED:

- Encender speaker (sonido de explosión).

- Mostrar patrón de “X” o “boom” en el display.

## Actividad 5

from microbit import *
import utime

Estados
STATE_CONFIG = 0
STATE_ARMED = 1
STATE_EXPLODED = 2

Variables
current_state = STATE_CONFIG
time_left = 20   # tiempo inicial
start_time = utime.ticks_ms()

while True:
    if current_state == STATE_CONFIG:
        # Mostrar tiempo configurado
        display.scroll(str(time_left))

        # Ajustar tiempo
        if button_a.was_pressed():  # UP
            if time_left < 60:
                time_left += 1
        if button_b.was_pressed():  # DOWN
            if time_left > 10:
                time_left -= 1

        # Armar bomba con shake
        if accelerometer.was_gesture("shake"):
            start_time = utime.ticks_ms()
            current_state = STATE_ARMED

    if current_state == STATE_ARMED:
        # Mostrar tiempo restante
        display.show(str(time_left))

        # Cada segundo, reducir el tiempo
        if utime.ticks_diff(utime.ticks_ms(), start_time) >= 1000:
            start_time = utime.ticks_ms()
            time_left -= 1

        # Si llega a 0 → explota
        if time_left <= 0:
            current_state = STATE_EXPLODED

        # Botón touch → volver a CONFIG
        if pin_logo.is_touched():
            time_left = 20
            current_state = STATE_CONFIG

    if current_state == STATE_EXPLODED:
        # Mostrar explosión y sonar
        display.show(Image.SKULL)
        sleep(2000)

        # Volver a CONFIG al tocar
        if pin_logo.is_touched():
            time_left = 20
            current_state = STATE_CONFIG

Vectores de prueba básicos

Vector de prueba 1
- Condición inicial: Estado = CONFIG, tiempo = 20.
- Evento: Botón A presionado 5 veces.
- Esperado: Tiempo = 25, se muestra en pantalla.
- Resultado: todo funciona correcto

Vector de prueba 2
- Condición inicial: Estado = CONFIG, tiempo = 20.
- Evento: Gesto shake (ARMED).
- Esperado: Cambia a estado ARMADO, inicia cuenta regresiva en pantalla.
- Resultado: todo funciona correcto

Vector de prueba 3
- Condición inicial: Estado = ARMADO, tiempo = 3.
- Evento: Pasan 3 segundos.
- Esperado: Tiempo llega a 0 → cambia a EXPLODED, muestra calavera.
- Resultado: todo funciona correcto

Vector de prueba 4
- Condición inicial: Estado = EXPLODED.
- Evento: Tocar pin_logo.
- Esperado: Reinicia a CONFIG, tiempo vuelve a 20.
- Resultado: todo funciona correcto

## Actividad 6

1. Una máquina de estados es una forma de organizar la lógica de un programa dividiéndolo en estados definidos, donde cada estado responde a eventos específicos y realiza acciones.
Los cuatro componentes fundamentales que usamos en esta unidad son:

Estados: las diferentes situaciones en que puede estar el sistema (ej. CONFIG, ARMED, EXPLODED).
Eventos: sucesos que cambian el flujo (ej. botón presionado, tiempo transcurrido, shake).
Acciones: lo que hace el sistema en respuesta a un evento (ej. mostrar en display, sonar alarma).
Transiciones: el paso de un estado a otro en función de un evento.


2. La técnica de máquina de estados es útil porque permite atender varios eventos “al mismo tiempo” en un solo hilo de ejecución.

Con estados, el sistema revisa continuamente qué eventos ocurren y actúa en consecuencia.
Esto evita usar sleep(), que bloquea el programa y no permite responder a otros eventos mientras pasa el tiempo
Con una máquina de estados se puede contar el tiempo y, a la vez, atender botones u otros sensores.


3. reducir tiempo a la mitad con shake en cuenta regresiva

Para añadir esta funcionalidad:

En el estado ARMED, además de los eventos ya definidos, se agrega “si shake detectado” → acción: dividir el tiempo restante por 2.
El diagrama tendría una flecha desde STATE_ARMED hacia sí mismo con el evento shake y la acción time_left = time_left // 2.


4. Un vector de prueba es un conjunto de condiciones iniciales, un evento aplicado y el resultado esperado.
Sirve para comprobar que la máquina de estados responde correctamente a los eventos.
Es crucial porque permite detectar errores y asegurar que el comportamiento del sistema coincide con lo diseñado.


5. Lo más desafiante fue traducir el diagrama a código MicroPython.

El diagrama es más visual y sencillo de entender.
Pero al pasarlo a código, hay que cuidar detalles como el control del tiempo, la gestión de botones y las condiciones de cada transición.


6. Un bug fue que la cuenta regresiva no bajaba bien porque usé sleep() al inicio. Eso bloqueaba la lectura de botones.
Pensar en estados, eventos y transiciones me ayudó a ver que el tiempo debía manejarse con utime.ticks_ms() y no con sleep(), porque necesitaba seguir atendiendo eventos concurrentes.


7. Empecé con una versión simple (solo mostrar el tiempo en configuración).
Luego agregué el armado y la cuenta regresiva.
Finalmente añadí la explosión y el reinicio con touch.
Esto permitió ir probando cada parte y asegurar que funcionaba antes de avanzar.


8. El patrón de máquina de estados se puede aplicar en:

Videojuegos simples en micro:bit (ej. un juego de plataformas con estados: menú, jugando, pausa, game over).
Sistemas interactivos como luces de navidad con distintos modos.
Escape rooms digitales donde distintos sensores cambian el estado del juego.
Robótica (ej. robot con estados: avanzar, girar, detenerse).

