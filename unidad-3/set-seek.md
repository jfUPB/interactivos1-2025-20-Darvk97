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

