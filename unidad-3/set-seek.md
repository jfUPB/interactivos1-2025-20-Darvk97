# Unidad 3

## 🔎 Fase: Set + Seek


No hay evidencias de esta actividad. Esta nota la deja el profesor

# Actividad 1:

    from microbit import *
    import utime

    display.clear()

    class Semaforo:
    def __init__(self, x, y, tr, ty, tg):
        self.x = x               
        self.y = y                
        self.tr = tr              
        self.ty = ty             
        self.tg = tg              
        self.startTime = utime.ticks_ms()
        self.state = 'WaitInRed' 
        display.set_pixel(self.x, self.y, 9)  
    def update(self):
        now = utime.ticks_ms()

        if self.state == 'WaitInRed':
            if utime.ticks_diff(now, self.startTime) >= self.tr:
                display.set_pixel(self.x, self.y, 0)
                display.set_pixel(self.x, self.y+1, 5)
                self.startTime = now
                self.state = 'WaitInYellow'

        elif self.state == 'WaitInYellow':
            if utime.ticks_diff(now, self.startTime) >= self.ty:
                display.set_pixel(self.x, self.y+1, 0)
                display.set_pixel(self.x, self.y+2, 2)
                self.startTime = now
                self.state = 'WaitInGreen'

        elif self.state == 'WaitInGreen':
            if utime.ticks_diff(now, self.startTime) >= self.tg:
                display.set_pixel(self.x, self.y+2, 0)
                display.set_pixel(self.x, self.y, 9)
                self.startTime = now
                self.state = 'WaitInRed'
                
    semaforo1 = Semaforo(0, 0, 5000, 2000, 3000)
    semaforo2 = Semaforo(2, 0, 3000, 1000, 2000)
    semaforo3 = Semaforo(4, 0, 4000, 3000, 2000)

    while True:
        semaforo1.update()
        semaforo2.update()
        semaforo3.update()

¿Qué ventajas tiene usar una clase (class) en este caso para representar un semáforo?
R//: organiza mejor el codigo, evita la repeticion de instrucciones y puede crear semaforos nuevos e independientes

¿Puedes ver cómo la técnica de programación con máquinas de estado y el uso de funciona no bloqueantes te permite que varios semáforos funcionen al mismo tiempo?
R//: si, ya que permite que se actualicen sin alterarse entre si haciendo que puedan funcionar al mismo tiempo


# Actividad 2: 

    Imports go at the top
    from microbit import *
    import utime

    display.clear()

    class BombTask:
    def __init__(self):
        self.PASSWORD = ['A','B','A']
        self.key = ['']*len(self.PASSWORD)
        self.keyindex = 0
        self.count = 20
        self.startTime = utime.ticks_ms()
        self.state = 'CONFIG'
        display.clear()
        display.show(self.count,wait=False)

    def update(self):
        if self.state == 'CONFIG':
            if button_a.was_pressed():
                self.count = min(self.count+1,60)
                display.show(self.count,wait=False)

            if button_b.was_pressed():
                self.count = max(10,self.count-1)
                display.show(self.count, wait=False)

            if accelerometer.was_gesture('shake'):
                self.startTime = utime.ticks_ms()
                self.state = 'ARMED'

        elif self.state == 'ARMED':
            if utime.ticks_diff(utime.ticks_ms(),self.startTime) > 1000:
                self.startTime = utime.ticks_ms()
                self.count = self.count - 1
                display.show(self.count,wait=False)
                if self.count == 0:
                    display.show(Image.SKULL)
                    self.state = 'EXPLODED'

            if button_a.was_pressed():
                self.key[self.keyindex] = 'A'
                self.keyindex = self.keyindex + 1

            if button_b.was_pressed():
                self.key[self.keyindex] = 'B'
                self.keyindex = self.keyindex + 1

            if self.keyindex == len(self.key):

                passIsOK = True
                for i in range(len(self.key)):
                    if self.key[i] != self.PASSWORD[i]:
                        passIsOK = False
                        break;
                if passIsOK == True:
                    self.count = 20
                    display.show(self.count,wait=False)
                    self.keyindex = 0
                    self.state = 'CONFIG'
                else:
                    self.keyindex = 0

        elif self.state == 'EXPLODED':
            if pin_logo.is_touched():
                self.count = 20
                display.show(self.count,wait=False)
                self.startTime = utime.ticks_ms()
                self.state = 'CONFIG'

        bombTask = BombTask()

        while True:
        bombTask.update()

en el programa funciona correcto pero el codigo no me funcionó en unas lineas y revisandolo con el de la actividad lo pude corregir

# Actividad 3:

    # Imports go at the top
    from microbit import *
        import utime

    display.clear()

    class Event:
        def __init__(self):
            self.value = 0

    def set(self,_val):
        self.value = _val

    def clear(self):
        self.value = 0

    def read(self):
        return self.value

    class SerialTask:
        def __init__(self):
            uart.init(baudrate=115200)

    def update(self):
        if uart.any():
            data = uart.read(1)
            if data:
                if data[0] == ord('A'):
                    event.set('A')
                elif data[0] == ord('B'):
                    event.set('B')
                elif data[0] == ord('S'):
                    event.set('S')
                elif data[0] == ord('T'):
                    event.set('T')


    class ButtonTask:
        def __init__(self):
            pass

    def update(self):
        if button_a.was_pressed():
            event.set('A')
        elif button_b.was_pressed():
            event.set('B')
        elif accelerometer.was_gesture('shake'):
            event.set('S')
        elif pin_logo.is_touched():
            event.set('T')

    class BombTask:
        def __init__(self):
            self.PASSWORD = ['A','B','A']
            self.key = ['']*len(self.PASSWORD)
            self.keyindex = 0
            self.count = 20
            self.startTime = utime.ticks_ms()
            self.state = 'CONFIG'
            display.clear()
            display.show(self.count,wait=False)

    def update(self):
        if self.state == 'CONFIG':
            if event.read() == 'A':
                event.clear()
                self.count = min(self.count+1,60)
                display.show(self.count,wait=False)

            if event.read() == 'B':
                event.clear()
                self.count = max(10,self.count-1)
                display.show(self.count, wait=False)

            if event.read() == 'S':
                event.clear()
                self.startTime = utime.ticks_ms()
                self.state = 'ARMED'

        elif self.state == 'ARMED':
            if utime.ticks_diff(utime.ticks_ms(),self.startTime) > 1000:
                self.startTime = utime.ticks_ms()
                self.count = self.count - 1
                display.show(self.count,wait=False)
                if self.count == 0:
                    display.show(Image.SKULL)
                    self.state = 'EXPLODED'

            if event.read() == 'A':
                event.clear()
                self.key[self.keyindex] = 'A'
                self.keyindex = self.keyindex + 1

            if event.read() == 'B':
                event.clear()
                self.key[self.keyindex] = 'B'
                self.keyindex = self.keyindex + 1

            if self.keyindex == len(self.key):

                passIsOK = True
                for i in range(len(self.key)):
                    if self.key[i] != self.PASSWORD[i]:
                        passIsOK = False
                        break;
                if passIsOK == True:
                    self.count = 20
                    display.show(self.count,wait=False)
                    self.keyindex = 0
                    self.state = 'CONFIG'
                else:
                    self.keyindex = 0

        elif self.state == 'EXPLODED':
            if event.read() == 'T':
                event.clear()
                self.count = 20
                display.show(self.count,wait=False)
                self.startTime = utime.ticks_ms()
                self.state = 'CONFIG'

    bombTask = BombTask()
    serialTask = SerialTask()
    buttonTask = ButtonTask()
    event = Event()

    while True:
        serialTask.update()
        buttonTask.update()
        bombTask.update()

# Actividad 4: 

    let port;
    let connectBtn;
    let connectionInitialized = false;

    let validChars = "ABST";

    function setup() {
      createCanvas(400, 400);
      background(220);
      port = createSerial();
      connectBtn = createButton("Connect to micro:bit");
      connectBtn.position(80, 300);
      connectBtn.mousePressed(connectBtnClick);
    }

    function draw() {
      background(220);
      if (port.opened() && !connectionInitialized) {
        port.clear();
        connectionInitialized = true;
      }

      textAlign(CENTER);
      text("Press A,B,S,T to simulate micro:bit keys", width / 2, height / 2);




      if (!port.opened()) {
        connectBtn.html("Connect to micro:bit");
      } else {
        connectBtn.html("Disconnect");
      }
    }

    function keyPressed() {
      keyValue = key.toUpperCase();
      if(validChars.includes(keyValue)){
        console.log(keyValue);
        port.write(keyValue);
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

# Actividad 5: 
Construye el modelo de la bomba 3.0. Como ya tienes el código puedes tener un modelo muy preciso.
Crear una tabla con los vectores de prueba. La tabla debe tener 4 columnas por vector y puedes agrupar vectores en un gran vector. Las columnas son:
Estado inicial
Evento disparador
Acciones
Estado final

R//:
