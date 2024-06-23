# PauReyes999-Practica7ProcessadorsDigitals-
# Práctica 7: Buses de Comunicación III (I2S)

## Objetivo

El objetivo de la práctica es describir el funcionamiento del bus I2S y realizar ejercicios prácticos para comprender su funcionamiento.

## Introducción Teórica

El protocolo de comunicación I2S se utiliza para transferir señales de sonido digitales. En esta práctica, reproduciremos música desde la memoria interna y desde una tarjeta SD externa, además de comparar diferentes microcontroladores para entender por qué preferimos el ESP32 para proyectos I2S.

## El Protocolo de Comunicación I2S

I2S (Inter-Integrated Circuit Sound) es un estándar para conectar dispositivos de audio digital, desarrollado por Philips Semiconductors en 1986. Tanto el ESP32 como el ESP8266 soportan I2S, a diferencia de la mayoría de las placas Arduino que requieren componentes externos para lograr lo mismo.

### ¿Por qué Necesitamos el Protocolo I2S?

Para reproducir un archivo de audio digital con un microcontrolador, debemos considerar toda la cadena de audio digital, desde el almacenamiento del archivo en una tarjeta SD hasta la conversión de la señal digital a analógica mediante un DAC (Convertidor de Digital a Analógico).

### Problemas con Diferentes Microcontroladores

- **Arduino y ESP8266**: No tienen un DAC interno, por lo que requieren componentes externos.
- **ESP32**: Tiene un DAC interno de 8 bits, lo cual puede llevar a una pérdida de calidad en señales de 16 bits.

El protocolo I2S soluciona esto, soportando entre 4 y 32 bits de datos por muestra.

## Protocolo de Comunicación I2S en Detalle

### Conexión de 3 Cables I2S

1. **SCK (Serial Clock)**: Define la frecuencia del reloj serial.
   - Frecuencia = Frecuencia de muestreo * Bits por canal * Número de canales
   - Ejemplo: 44.1 kHz * 16 bits * 2 canales = 1.411 MHz

2. **WS (Word Select)**: Diferencia entre el canal izquierdo (WS = 0) y el derecho (WS = 1).

3. **SD (Serial Data)**: Transmite la carga útil en complemento a 2, enviando el bit más significativo primero (MSB primero).

### Componentes de la Red I2S

Las redes I2S pueden tener un dispositivo maestro y múltiples receptores o transmisores. El maestro controla las líneas SCK y WS.

### Diagrama de Temporización I2S

Muestra cómo se envían los datos en cada ciclo de reloj y cómo WS cambia antes de que se transmita el MSB.

## Placa de Conexión de Audio MAX98357 I2S

Esta placa decodifica la señal I2S en una señal analógica y amplifica la señal para el altavoz. Es un amplificador de clase D con una tasa de ganancia ajustable y soporta frecuencias de muestreo entre 8 kHz y 96 kHz.

## Ejercicio Práctico 1: Reproducción desde Memoria Interna

### Hardware Requerido

- ESP32
- Placa de conexión de audio MAX98357 I2S
- Altavoz

### Conexiones

1. **ESP32**:
   - GPIO 26 → DIN de MAX98357
   - GPIO 25 → BCLK de MAX98357
   - GPIO 22 → LRC de MAX98357

2. **MAX98357**:
   - Vin → 3.3V
   - GND → GND

### Código

```cpp
#include "AudioGeneratorAAC.h"
#include "AudioOutputI2S.h"
#include "AudioFileSourcePROGMEM.h"
#include "sampleaac.h"

AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;

void setup() {
  Serial.begin(115200);
  in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
  aac = new AudioGeneratorAAC();
  out = new AudioOutputI2
