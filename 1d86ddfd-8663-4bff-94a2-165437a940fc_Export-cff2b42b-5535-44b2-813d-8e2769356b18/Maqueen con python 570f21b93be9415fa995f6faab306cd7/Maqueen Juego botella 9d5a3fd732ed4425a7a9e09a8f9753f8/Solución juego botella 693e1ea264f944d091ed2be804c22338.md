# Solución juego botella

```python
"""
Juego botella
"""

from microbit import *
from maqueen import *

rojo = (255,0,0)
amarillo = (255,255,0)
verde = (0,255,0)
azul = (0,0,255)

retardo = 5000

np = Maqueen().rgbleds
motor = Maqueen().set_motor

for i in range(4):
        np[i] = rojo
        np.show()
    
while True:
    if button_a.was_pressed():
        for i in range(4):
            np[i] = amarillo
            np.show()
        motor(0,0)
        motor(1,100)
        sleep(retardo)
        estado = True
        while estado:
            distancia = Maqueen().read_distance()
            if distancia > 0 and distancia < 10:
                for i in range(4):
                    np[i] = azul
                    np.show()
                Maqueen().motor_stop_all()
                estado = False
            else:
                for i in range(4):
                    np[i] = verde
                    np.show()
                motor(0,255)
                motor(1,255)
                #estado = True
    if button_b.was_pressed():
        for i in range(4):
            np[i] = rojo
            np.show()
```

## Modificación *randint*

- El juego empieza pulsando el botón B: se ponen los LEDs en rojo y se genera nº al azar.
- Al pulsar A se pone a dar vueltas hasta el tiempo del número al azar.

```python
"""
Juego botella, con retardo al azar entre 2000 y 10000
"""

from microbit import *
from maqueen import *
from random import randint

rojo = (255,0,0)
amarillo = (255,255,0)
verde = (0,255,0)
azul = (0,0,255)

np = Maqueen().rgbleds
retardo = 0

motor = Maqueen().set_motor
    
while True:
    if button_a.was_pressed() and retardo > 0:
        for i in range(4):
            np[i] = amarillo
            np.show()
        motor(0,0)
        motor(1,100)
        sleep(retardo)
        estado = True
        while estado:
            distancia = Maqueen().read_distance()
            if distancia >0 and distancia < 10:
                for i in range(4):
                    np[i] = azul
                    np.show()
                Maqueen().motor_stop_all()
                estado = False
            else:
                for i in range(4):
                    np[i] = verde
                    np.show()
                motor(0,255)
                motor(1,255)
                estado = True
    if button_b.was_pressed():
        retardo = randint(2000,10000)
        for i in range(4):
            np[i] = rojo
            np.show()
```