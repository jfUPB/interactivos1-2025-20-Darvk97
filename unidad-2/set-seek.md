# Unidad 2

## 🔎 Fase: Set + Seek



NO ESTÁ ELABORADO: nota puesta por Juan Franco

## Fase: Set + Seek
## Actividad 1

Estados:
- Init
- WaitTimeout

Eventos/inputs:
- Inicio del objeto
- Paso del tiempo (cuando el intervalo se cumple)

Acciones:
- Guardar el tiempo actual
- Cambiar el valor del píxel (encender/apagar)
- Mostrar el píxel en el display

## Actividad 2

    from microbit import *
    import utime

    class Semaforo:
        def _init_(self, intervalo_rojo, intervalo_verde, intervalo_amarillo):
            self.state = "Rojo"
            self.startTime = utime.ticks_ms()
            self.intervalos = {
                "Rojo": intervalo_rojo,
                "Verde": intervalo_verde,
                "Amarillo": intervalo_amarillo
            }

    def update(self):
        for y in range(3):
            display.set_pixel(0, y, 0)

        if self.state == "Rojo":
            display.set_pixel(0, 0, 9)
        elif self.state == "Verde":
            display.set_pixel(0, 2, 9)
        elif self.state == "Amarillo":
            display.set_pixel(0, 1, 9)

        if utime.ticks_diff(utime.ticks_ms(), self.startTime) > self.intervalos[self.state]:
            self.startTime = utime.ticks_ms()
            if self.state == "Rojo":
                self.state = "Verde"
            elif self.state == "Verde":
                self.state = "Amarillo"
            elif self.state == "Amarillo":
                self.state = "Rojo"

    semaforo = Semaforo(3000, 3000, 1000)

    while True:
        semaforo.update()

Estados:
- Rojo
- Verde
- Amarillo

Eventos/inputs:
- Paso del tiempo (cuando se cumple el intervalo del estado actual)

Acciones:
- Encender el LED correspondiente
- Apagar los demás LEDs
- Cambiar al siguiente estado

## Actividad 3 

Explica por qué decimos que este programa permite realizar de manera concurrente varias tareas.
R//: Porque la máquina de estados gestiona dos tipos de eventos en paralelo:
- El ciclo automático de imágenes (según intervalos de tiempo).
- La respuesta inmediata al botón A en cualquier momento.
De esta manera, el programa atiende tanto la secuencia como la interacción del usuario sin que una bloquee a la otra.

Estados del programa
- STATE_INIT → Inicializa la secuencia (muestra cara feliz).
- STATE_HAPPY → Muestra cara feliz.
- STATE_SMILE → Muestra cara sonriente.
- STATE_SAD → Muestra cara triste.

Eventos del programa
- Paso del tiempo → Cuando se cumple el intervalo de cada estado.
- button_a.was_pressed() → Evento de pulsación del botón A.

Acciones del programa
- Mostrar una imagen en pantalla (display.show(...)).
- Reiniciar el temporizador (start_time = utime.ticks_ms()).
- Actualizar el intervalo según el estado.
- Cambiar al siguiente estado.

Vectores de prueba:

Vector de prueba 1
- Condición inicial: Estado = STATE_HAPPY, tiempo = 0.
- Evento: Dejar pasar 1500 ms.
- Esperado: Cambiar a STATE_SMILE, mostrar imagen SMILE.
- Resultado real: Igual al esperado 

Vector de prueba 2
- Condición inicial: Estado = STATE_SMILE.
- Evento: Presionar botón A.
- Esperado: Cambiar a STATE_HAPPY, mostrar imagen HAPPY.
- Resultado real: Igual al esperado 

Vector de prueba 3
- Condición inicial: Estado = STATE_SAD, tiempo = 0.
- Evento: Pasan 2000 ms.
- Esperado: Cambiar a STATE_HAPPY, mostrar imagen HAPPY.
- Resultado real: Igual al esperado
