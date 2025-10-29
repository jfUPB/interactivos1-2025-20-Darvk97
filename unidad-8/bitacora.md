
# Evidencias de la unidad 8

## Actividad 1 ##

1. Documenta los referentes visuales que te inspiren.
R//: me basé en los conciertos de bandas de metal como peelingflesh, spite, snuffed on sight, chelsea grin, etc para hacer una simulacion e un mosh pit con particulas y distintos temas sonando para que se vea de una manera mas agradable

2. Define el concepto de las visuales que quieres crear.
R//: cuando suene cada cancion se verá de fondo las potadas de los albumnes correpondientes de cada tema con particulas interactivas que cambiaran de color simulando un concierto de la banda que suene en cuestion y las particulas siendo una especie de pogo multicolor que se mueve por la pantalla 

3. Explica cómo el móvil y el micro:bit controlarán las visuales.
R//: al presionar los botones a y b (tanto en el micro:bit como con el celular en la pantalla) se cambiara de cancion, y al girar los dispositivos las particulas se moveran al rededor de la pantalla del Pc simulando un pogo y cambiando de color en el proceso

4. Haz un bocetos de todas las interfaces del sistema.
R//: - PC: muestra las visuales y como se vera todo con las particulas, el movimiento, etc.
- Celular y micro:bit: ambos con los botones A y B (en el caso del celular se agregaran dichos botones) controlaran los temas que se reproducen cambiando entre uno y otro mostrando la foto del disco que se reproduce y al usar el giroscopio o detetctor de rotacion de los dispositivos se moveran las particulas de un lado a otro y con el movimiento automaticamente cambiaran de color sinronizandose con la musica

6. Haz un diagrama que explique cómo se comunicarán los diferentes componentes del sistema
R//:


        Móvil con sensores de movimiento
                ↓
           datos de inclinación
           (Socket o Bluetooth)
                ↓
              PC con p5.js / servidor Node.js
                ↑
        Micro:bit (botones A y B)

