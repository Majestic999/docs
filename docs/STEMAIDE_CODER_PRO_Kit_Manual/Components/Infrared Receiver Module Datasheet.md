# Infrared Receiver Module (VS1838B) — Technical Datasheet

## General Description

The VS1838B is a miniaturized infrared receiver module designed for optical wireless remote control systems. It integrates an optical PIN photodiode, preamplifier, automatic gain control (AGC), bandpass filter, and demodulator within a shielded epoxy package. It demodulates 38 kHz pulse-coded infrared signals and outputs active-LOW TTL digital pulses directly to the microcontroller [E1].

### Native 3.3V Operation & RMT Hardware Acceleration
The VS1838B operates across a supply range of **2.7V to 5.5V DC** [E1]. When interfacing with the **Espressif ESP32-DevKitC V4**, powering the module from the **`3V3` power rail** produces **native 3.3V LVTTL output**, allowing direct connection to the ESP32 without level shifting [E1/E5].

Furthermore, modern ESP32 software uses the **`IRremote` v4+** library, which leverages the ESP32's hardware **RMT (Remote Control Transceiver) peripheral** for DMA-backed background decoding without CPU timer interrupts [E4].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Receiver IC** | VS1838B (Metal-shielded IR receiver) | [E1] |
| **Carrier Frequency** | 38.0 kHz | [E1] |
| **Operating Voltage ($V_{CC}$)** | 2.7 V to 5.5 V DC (**Operated at 3.3 V on ESP32**) | [E1/E5] |
| **Supply Current** | 0.4 mA to 1.5 mA (Low power consumption) | [E1] |
| **Peak Detection Wavelength**| 940 nm (standard infrared) | [E1] |
| **Reception Distance** | Up to 18 metres | [E1] |
| **Reception Angle** | ±45° (90° conical beam) | [E1] |
| **Output State** | Active-LOW digital pulses (Demodulated envelope) | [E1] |
| **Hardware Driver** | ESP32 Hardware RMT Peripheral | [E4] |

## Pinout & Wiring

```
         VS1838B Module Pinout
              ┌─────────┐
              │  [:::]  │ (Front Sensor Dome)
              └─────────┘
               │   │   │
               1   2   3
              OUT GND VCC
```

| Pin # | Label | ESP32 Connection | Description |
|---|---|---|---|
| 1 | **OUT** | **GPIO 15** (or GPIO 4) | Demodulated 3.3V active-LOW digital data line [E5] |
| 2 | **GND** | **GND** | Power ground reference [E1] |
| 3 | **VCC** | **3V3 Rail** | 3.3V regulated DC power [E5] |

```
   ESP32-DevKitC V4                      VS1838B IR Receiver
  ┌────────────────┐                     ┌──────────────────┐
  │            3V3 ├────────────────────►│ VCC (Pin 3)      │
  │        GPIO 15 ├◄────────────────────┤ OUT (Pin 1)      │
  │            GND ├─────────────────────┤ GND (Pin 2)      │
  └────────────────┘                     └──────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Modern `IRremote` v4.x Decoding

```cpp
#include <IRremote.hpp>

const int IR_RECEIVE_PIN = 15; // Uses ESP32 RMT peripheral

void setup() {
  Serial.begin(115200);
  // Initialize IR receiver using ESP32 hardware RMT
  IrReceiver.begin(IR_RECEIVE_PIN, ENABLE_LED_FEEDBACK);
  Serial.println("ESP32 VS1838B IR Receiver Ready.");
}

void loop() {
  if (IrReceiver.decode()) {
    Serial.println("----------------------------------------");
    Serial.printf("Protocol: %s | Command: 0x%02X | Address: 0x%04X\n",
                  getProtocolString(IrReceiver.decodedIRData.protocol),
                  IrReceiver.decodedIRData.command,
                  IrReceiver.decodedIRData.address);
    Serial.printf("Raw 32-bit Hash: 0x%08X\n", IrReceiver.decodedIRData.decodedRawData);

    IrReceiver.resume(); // Receive the next value
  }
}
```

## Source References
- Vishay Semiconductors Infrared Receiver Modules: https://www.vishay.com/ir-receiver-modules/
- IRremote Library Repository by Armin Joachimsmeyer: https://github.com/Arduino-IRremote/Arduino-IRremote
