# P8_AlexandrePascual_MartiVila
Participants: Pascual Deza i Martí Vila 

# Comunicació Serie (UART)


## Introducció

En aquesta pràctica s’estudia la comunicació sèrie asíncrona mitjançant el protocol **UART** (Universal Asynchronous Receiver Transmitter), àmpliament utilitzat en sistemes embeguts i microcontroladors com l’ESP32.

En pràctiques anteriors ja s’ha utilitzat UART per comunicar-se amb el monitor sèrie (per exemple, `Serial.println("...")`), però en aquesta ocasió s’aprofundeix en el seu funcionament a baix nivell, implementant una comunicació bidireccional entre UART0 (PC) i UART2 de l’ESP32.



## Objectius de la pràctica

1. Aprendre el funcionament de la comunicació UART a l’ESP32.  
2. Implementar un “eco” entre UART0 i UART2.  
3. Visualitzar l’intercanvi de dades entre terminals connectats mitjançant UART.  
4. Identificar la utilitat de UART en connexions amb mòduls com GPS o GPRS/GSM.



## Bucle de comunicació UART2

### Descripció

S’estableix un bucle de comunicació en què les dades que arriben al terminal UART0 (RXD0, que està connectat al PC) s’envien a UART2 (TXD2). Al mateix temps, la informació rebuda a UART2 (RXD2) es reenvia de nou al terminal UART0, fent que la comunicació sigui visible al monitor sèrie.


### *Codi(sin modificar):*

```c++
#include <Arduino.h>

#define BUF_SIZE 1024
uint8_t data_rx0[BUF_SIZE];
uint8_t data_rx2[BUF_SIZE];

void setup() {
  // Inicialitzar el monitor serial (UART0)
  Serial.begin(115200);  // UART0 para comunicación con el PC
  
 
  Serial.println("Iniciando la comunicación...");
  
  // Configuració de UART2 utilitzant pins GPIO16 (RXD2) y GPIO17 (TXD2)
  const int RXD2_PIN = 16;
  const int TXD2_PIN = 17;
  Serial2.begin(115200, SERIAL_8N1, RXD2_PIN, TXD2_PIN);  // UART2 con RXD2_PIN y TXD2_PIN
  
  // Confirmació de que UART2 s'ha iniciat correctament
  Serial.println("UART2 configurado y listo para comunicarse.");
}

void loop() {
  // Llegir dades de UART0 (RXD0) i reenviar a UART2 (TXD2)
  if (Serial.available()) {
    int len = Serial.readBytes(data_rx0, BUF_SIZE);
    if (len > 0) {
      Serial.println("Datos leídos desde UART0, reenviando a UART2...");
      Serial2.write(data_rx0, len);  // Redirigir datos a UART2
    }
  }

  // Llegir dades de UART2 (RXD2) i reenviar a UART0 (TXD0)
  if (Serial2.available()) {
    int len = Serial2.readBytes(data_rx2, BUF_SIZE);
    if (len > 0) {
      Serial.println("Datos leídos desde UART2, reenviando a UART0...");
      Serial.write(data_rx2, len);  // Redirigir datos a UART0
    }
  }
}
```

#### **Sortida:**

```
Iniciando la comunicación...
UART2 configurado y listo para comunicarse.
Datos leídos desde UART0, reenviando a UART2...
Datos leídos desde UART2, reenviando a UART0...
Hola mundo!
```

## Resum

Aquesta pràctica ha permès comprendre millor el funcionament de la comunicació UART a l’ESP32, aprofundint en la seva configuració a baix nivell. S’ha implementat un sistema d’eco entre UART0 i UART2 que permet visualitzar l’intercanvi de dades entre dispositius. Aquesta experiència demostra la importància i versatilitat del protocol UART en aplicacions reals com la connexió amb mòduls GPS o GSM/GPRS, reforçant el seu paper clau en entorns de sistemes embeguts.
