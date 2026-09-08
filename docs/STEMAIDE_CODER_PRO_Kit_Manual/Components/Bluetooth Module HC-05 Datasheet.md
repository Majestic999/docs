# Bluetooth Module HC-05 — Technical Datasheet

## General Description

The HC-05 is a classic Bluetooth 2.0 + EDR module supporting the Serial Port Profile (SPP) for transparent wireless serial communication. It can operate in either Master or Slave mode and is widely used for smartphone remote control and telemetry [E1].

### Architectural Migration to the ESP32 Platform
On the legacy Arduino UNO R3, the HC-05 required the slow, CPU-intensive `SoftwareSerial.h` library (which bit-banged UART on pins D2/D3) and a resistive voltage divider to step the Arduino's 5V TX down to the HC-05's 3.3V RX level [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**:
1. **Hardware UART2 Integration**: The HC-05 connects directly to the ESP32's **Hardware UART2 (`GPIO 16 RX2`, `GPIO 17 TX2`)**, eliminating SoftwareSerial completely [E1/E4].
2. **Native 3.3V Logic Parity**: Because both the ESP32 and HC-05 UART operate at 3.3V logic levels, **no voltage divider is required on the TX/RX lines** [E1/E5]!
3. **Onboard Emulation Capability**: The ESP32-WROOM-32D integrates a dual-mode Bluetooth Classic radio, allowing students to learn both physical module interfacing AND native on-chip Bluetooth serial emulation using `BluetoothSerial.h` [E1/E4].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Bluetooth Standard** | Bluetooth v2.0 + EDR (Classic SPP) | [E1] |
| **Operating Voltage ($V_{CC}$)** | 3.6 V to 6.0 V DC (**Connect to 5V Power Rail**) | [E1/E3] |
| **UART Logic Level** | **3.3 V LVTTL** (Direct connection to ESP32 UART2) | [E1/E5] |
| **Default Baud Rate** | 9600 bps, 8 data bits, 1 stop bit, no parity (8N1) | [E1] |
| **AT Command Baud Rate** | 38400 bps (enabled by holding EN/KEY pin HIGH at power-up) | [E1] |
| **Operating Current** | 30 mA to 40 mA (Connected); ~8 mA (Idle pairing) | [E1] |
| **Range** | ~10 metres (Class 2, +4 dBm) | [E1] |

## Pinout & Wiring Comparison

| Pin Label | Function | Legacy Arduino UNO Connection | ESP32-DevKitC V4 Connection | Notes |
|---|---|---|---|---|
| **VCC** | Power (3.6–6V) | 5V | **5V Power Rail** | Module powered from 5V rail [E3] |
| **GND** | Ground | GND | **GND** | Common ground reference [E1] |
| **TXD** | UART Transmit (3.3V) | D2 (SoftwareSerial RX) | **GPIO 16 (Hardware RX2)** | Direct 3.3V serial data into ESP32 [E5] |
| **RXD** | UART Receive (3.3V) | D3 (via 1k/2k divider) | **GPIO 17 (Hardware TX2)** | Direct 3.3V serial data from ESP32 [E5] |
| **STATE** | Connection Status | Unused | Optional GPIO | HIGH when Bluetooth paired [E1] |
| **EN / KEY**| AT Command Mode | Unused | Optional GPIO | Pull HIGH during boot for AT mode [E1] |

```
   ESP32-DevKitC V4                      HC-05 Bluetooth Module
  ┌────────────────┐                     ┌────────────────────┐
  │             5V ├────────────────────►│ VCC (5V)           │
  │        GPIO 16 ├◄────────────────────┤ TXD (3.3V Level)   │ (Direct UART2 RX)
  │        GPIO 17 ├────────────────────►┤ RXD (3.3V Level)   │ (Direct UART2 TX)
  │            GND ├─────────────────────┤ GND                │
  └────────────────┘                     └────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — HardwareSerial Serial2 Bridge

```cpp
#include <HardwareSerial.h>

HardwareSerial SerialBT(2); // Use ESP32 Hardware UART2

void setup() {
  Serial.begin(115200);                                  // USB Monitor
  SerialBT.begin(9600, SERIAL_8N1, 16, 17);              // RX2=GPIO 16, TX2=GPIO 17
  Serial.println("ESP32 Hardware UART2 to HC-05 Bridge Ready.");
}

void loop() {
  // Read from HC-05 and echo to USB Monitor
  while (SerialBT.available()) {
    char c = SerialBT.read();
    Serial.write(c);
  }
  // Read from USB Monitor and send to HC-05
  while (Serial.available()) {
    char c = Serial.read();
    SerialBT.write(c);
  }
}
```

## Source References
- ITEAD Studio HC-05 Bluetooth Module Datasheet: https://www.itead.cc
- Espressif HardwareSerial Reference: https://docs.espressif.com/projects/arduino-esp32/en/latest/api/uart.html
