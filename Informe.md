# __Conversor Analógico-Digital (ADC)__
  
 *El código presentado está diseñado para gestionar las conversiones analógico-digitales (ADC), aprovechando el ADC integrado en microcontroladores, específicamente el Raspberry Pi Pico RP2040.*
 
## _Función del código_

 *El objetivo principal de este código es adquirir una señal analógica a través de un pin del microcontrolador y convertirla a diferentes representaciones digitales para su procesamiento, monitorización, o control.*

***Modularidad:*** *La implementación utiliza una clase (ConversorADC) para encapsular la funcionalidad del ADC, lo que facilita la reutilización y el mantenimiento del código, proporcionando una arquitectura de software robusta.*

*El código ofrece la capacidad de obtener el valor analógico en cuatro formatos distintos:*

- ​***Valor Raw:***  *El valor entero sin procesar del ADC (0 a 65535, 16 bits).*

- ***Voltaje:*** *El valor convertido a su equivalente en voltios (0 a 3.3V).*

- ***Porcentaje:*** *El valor escalado a un rango de 0% a 100%.*

- ***12 Bits:*** *El valor reducido a la resolución nativa de 12 bits (0 a 4095) mediante una operación de desplazamiento de bits.*

## _¿Cómo Trabaja el Código?_

*El código se estructura en torno a la clase ConversorADC y un bucle de muestreo continuo:*

​ ***Inicialización (_init_)***

- ***​Módulos:***  *Importa la clase ADC y Pin del módulo machine para interactuar con el hardware, y time para la temporización.*

- ***​Configuración:*** *Al crear una instancia de ConversorADC(26), se configura el Pin 26 como entrada analógica.*

- ***​Parámetros:*** *Se establecen self.resolucion = 16 (el rango completo de salida digital) y self.vref = 3.3 (el voltaje de referencia, que determina el rango máximo de la medida).*

***Búcle principal***
- ***Instanciación:*** *Se crea el objeto adc = ConversorADC(26).*

- ***​Muestreo:*** *El bucle while True realiza lecturas secuenciales de todas las representaciones (raw, voltaje, porcentaje, 12 bits) cada vez que itera.*

- ***​Salida:*** *Utiliza la función print para mostrar los valores formateados por la consola.*

- ***​Temporización:*** *La línea time.sleep(0.5) introduce un retardo de 500 ms, lo que resulta en una frecuencia de muestreo de 2 Hz (dos muestras por segundo).*

## _Código:_


 ```python
 from machine import ADC, Pin
import time

class ConversorADC:
    def __init__(self, pin_adc=26):
        self.adc = ADC(Pin(pin_adc))
        self.resolucion = 16  
        self.vref = 3.3       
    
    def leer_raw(self):
        """Lee valor ADC sin procesar (0-65535)"""
        return self.adc.read_u16()
    
    def leer_voltaje(self):
        """Convierte a voltaje (0-3.3V)"""
        raw = self.leer_raw()
        return (raw * self.vref) / 65535
    
    def leer_porcentaje(self):
        """Convierte a porcentaje (0-100%)"""
        raw = self.leer_raw()
        return (raw / 65535) * 100
    
    def leer_12bits(self):
        """Convierte a resolución nativa de 12 bits (0-4095)"""
        raw = self.leer_raw()
        return raw >> 4  

adc = ConversorADC(26)

while True:
    raw = adc.leer_raw()
    voltaje = adc.leer_voltaje()
    porcentaje = adc.leer_porcentaje()
    bits12 = adc.leer_12bits()
    
    print(f"RAW: {raw:5d} | {voltaje:.2f}V | {porcentaje:.1f}% | 12b: {bits12:4d}")
    time.sleep(0.5)   