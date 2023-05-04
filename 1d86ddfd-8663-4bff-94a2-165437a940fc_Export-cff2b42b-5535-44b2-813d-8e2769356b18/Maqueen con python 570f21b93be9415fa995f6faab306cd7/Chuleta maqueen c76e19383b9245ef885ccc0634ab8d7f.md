# Chuleta maqueen

## Librería maqueen.py

Para programar el maqueen, primero hay que añadir esta librería al proyecto.

- maqueen.py
    
    ```python
    import microbit
    import machine
    import utime
    import neopixel
    
    class Maqueen:
    	"""
    	Python class for DFRobot Micro:maqueen platform
    	https://www.dfrobot.com/product-1783.html
    	Author: Krzysztof Sawicki <krzysztof@rssi.pl>
    	License: GNU
    	"""
    	def __init__(self):
    		self.rgbleds = neopixel.NeoPixel(microbit.pin15, 4)
    		print("MAQUEEN initialized")
    
    	def set_led(self, lednumber, value):
    		"""
    		Enable or disable the front LEDS
    		0 - left LED (P8)
    		1 - right LED (P12)
    		"""
    		if lednumber == 0:
    			microbit.pin8.write_digital(value)
    		elif lednumber == 1:
    			microbit.pin12.write_digital(value)
    
    	def read_distance(self):
    		"""
    		Reads distance from HC SR04 sensor
    		The result is in centimeters
    		Divider is taken from Makecode library for micro:maqueen
    		"""
    		maxtime = 25000
    		microbit.pin2.read_digital()  # just for setting PULL_DOWN on pin2
    		microbit.pin1.write_digital(0)
    		utime.sleep_us(2)
    		microbit.pin1.write_digital(1)
    		utime.sleep_us(10)
    		microbit.pin1.write_digital(0)
    
    		duration = machine.time_pulse_us(microbit.pin2, 1, maxtime)
    		distance = round(duration * 34 / 2 / 1000)
    		return distance
    
    	def read_patrol(self, which):
    		"""
    		Reads patrol sensor
    		"""
    		if which == 0:  # left
    			return microbit.pin13.read_digital()
    		elif which == 1:  # right
    			return microbit.pin14.read_digital()
    
    	def set_motor(self, motor, value):
    		"""
    		Controls motor
    		motor: 0 - left motor, 1 - right motor
    		value: -255 to +255, the sign means direction
    		"""
    		data = bytearray(3)
    		if motor == 0:  # left motor
    			data[0] = 0
    		else:
    			data[0] = 2  # right motor is 2
    		if value < 0:  # ccw direction
    			data[1] = 1
    			value = -1*value
    		data[2] = value
    		microbit.i2c.write(0x10, data, False)  # 0x10 is i2c address of motor driver
    
    	def motor_stop_all(self):
    		self.set_motor(0, 0)
    		self.set_motor(1, 0)
    ```
    

## LEDs

- Ejemplo parpadeo LEDs
    
    ```python
    """
    Programa para que parpadeen los LEDs rojos izquiero y derecho
    """
    
    from microbit import *
    from maqueen import *
    
    while True:
        Maqueen().set_led(0,1) #Enciende LED izquierdo
        sleep(1000)
        Maqueen().set_led(1, 1) #Enciende LED derecho
        Maqueen().set_led(0, 0) #Apaga LED izquierdo
        sleep(1000)
        Maqueen().set_led(1, 0) #Apaga LED derecho
    ```
    

## LEDs RGB (neopixel)

- Ejemplo LEDs RGB
    
    ```python
    """
    Programa para encender los LED neopixel
    """
    from microbit import *
    from maqueen import *
    
    np = Maqueen().rgbleds
    
    rojo = (255,0,0)
    verde = (00,255,0)
    azul = (0,0,255)
    amarillo = (255,255,0)
    apagado = (0,0,0)
    
    espera = 200
    
    while True:
        np[0] = rojo # Le paso el color rojo
        np.show() #Con este comando activo el LED
        np[1] = rojo
        np.show()
        np[2] = rojo 
        np.show() 
        np[3] = rojo
        np.show()
        sleep(espera)
        for i in range(4): #Con el bucle for no escribimos tantas líneas
            np[i] = azul
            np.show()
    ```
    

Web para seleccionar los colores: [https://www.w3schools.com/colors/colors_rgb.asp](https://www.w3schools.com/colors/colors_rgb.asp)

## Motores

- Ejemplo motores
    
    ```python
    """
    Un pasito pa'lante, un pasito pa'tras
    """
    
    from microbit import *
    from maqueen import *
    
    motor = Maqueen().set_motor #Truco del almendruco 
    
    while True:
        motor(0, 100) #Motor izquierdo a velocidad 100
        motor(1 ,100) #Motor derecho a velocidad 100
        sleep(1000)
        Maqueen().motor_stop_all() #Paro motores
        sleep(1000)
        motor(0, -100) #El signo negativo indica sentido contrario
        motor(1 ,-100)
        sleep(1000)
        Maqueen().motor_stop_all()
        sleep(1000)
    ```
    

## Ultrasonidos

- Ejemplo ultrasonidos
    
    ```python
    """
    Cambiar colores neopixel con el sensor de ultrasonidos
    """
    
    from microbit import *
    from maqueen import *
    
    np = Maqueen().rgbleds
    
    while True:
        distancia = Maqueen().read_distance() #Leo distancia
        if distancia >0 and distancia < 10:
            for i in range(4):
                np[i] = (255,0,0) #rojo
                np.show()
        elif distancia >=10 and distancia <20:
            for i in range(4):
                np[i] = (255,255,0) #amarillo
                np.show()
        elif distancia >=20 and distancia <30:
            for i in range(4):
                np[i] = (0,255,0) #verde
                np.show()
        elif distancia >=30:
            for i in range(4):
                np[i] = (0,0,0) #apagado
                np.show()
    ```
    

## Siguelíneas

- Ejemplo siguelíneas
    
    ```python
    """
    Cambiar colores neopixel con el sensor de ultrasonidos
    """
    
    from microbit import *
    from maqueen import *
    
    np = Maqueen().rgbleds
    
    while True:
        distancia = Maqueen().read_distance() #Leo distancia
        if distancia >0 and distancia < 10:
            for i in range(4):
                np[i] = (255,0,0) #rojo
                np.show()
        elif distancia >=10 and distancia <20:
            for i in range(4):
                np[i] = (255,255,0) #amarillo
                np.show()
        elif distancia >=20 and distancia <30:
            for i in range(4):
                np[i] = (0,255,0) #verde
                np.show()
        elif distancia >=30:
            for i in range(4):
                np[i] = (0,0,0) #apagado
                np.show()
    ```