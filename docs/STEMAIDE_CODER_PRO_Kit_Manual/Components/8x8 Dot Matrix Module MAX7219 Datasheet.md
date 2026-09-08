# 8x8 Dot Matrix Module (MAX7219) — Technical Datasheet

## General Description

An 8×8 red LED dot matrix display module driven by the MAX7219 serially interfaced LED driver IC. The MAX7219 integrates an on-chip BCD code-B decoder, multiplex scan circuitry, segment and digit drivers, and an 8×8 static RAM to hold display data, communicating with the host MCU via a 3-wire synchronous SPI interface [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, the MAX7219 requires 5.0V VCC to maintain adequate display brightness and internal driver headroom. While some clone ICs respond to 3.3V logic, the official Maxim MAX7219 specification requires $V_{IH\min} = 3.5\text{ V}$ at 5V $V_{CC}$ [E1]. For 100% glitch-free educational reliability, signals must pass through a **74HCT125 logic-level translator buffer** [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Driver IC** | Maxim MAX7219 (or compatible LED driver) | [E1] |
| **Matrix Format** | 64 individual LEDs arranged in an 8×8 grid | [E1] |
| **Emitted Colour** | Bright Red | [E1] |
| **Operating Voltage ($V_{CC}$)** | 4.0 V to 5.5 V DC (**Operated from 5V Power Rail**) | [E1] |
| **Logic Input High ($V_{IH\min}$)**| 3.5 V at $V_{CC} = 5.0\text{ V}$ (Official Maxim Spec) | [E1] |
| **Logic Input Low ($V_{IL\max}$)** | 0.8 V at $V_{CC} = 5.0\text{ V}$ | [E1] |
| **SPI Clock Frequency** | Up to 10 MHz hardware VSPI | [E1/E4] |
| **Peak Operating Current** | 150 mA to 330 mA (all LEDs illuminated at max intensity) | [E1] |
| **Quiescent Shutdown Current** | 150 µA | [E1] |
| **Dimensions** | 32 × 32 × 13 mm (module assembly) | [E1] |

## Interfacing Architecture & Logic Buffering

```
   ESP32-DevKitC V4             74HCT125 Level Buffer                MAX7219 Module
  ┌────────────────┐            ┌───────────────────┐             ┌──────────────────┐
  │             5V ├────────────┤ VCC            5V ├─────────────┤ VCC (5V Ext)     │
  │        GPIO 23 ├────────────┤ 1A             1Y ├─────────────┤ DIN (Data In)    │
  │        GPIO 18 ├────────────┤ 2A             2Y ├─────────────┤ CLK (SPI Clock)  │
  │        GPIO 27 ├────────────┤ 3A             3Y ├─────────────┤ CS / LOAD        │
  │            GND ├────────────┤ GND           GND ├─────────────┤ GND              │
  └────────────────┘            └───────────────────┘             └──────────────────┘
```

> [!CAUTION]
> **Prohibited Supply Voltage Degradation**:
> Placing a series silicon diode in line with the MAX7219 5V rail to drop voltage to 4.3V is strictly prohibited in STEMAIDE production guides. Use a proper 74HCT125 buffer to step 3.3V logic up to 5V [E5].

## Pinout

| Pin Label | Signal | ESP32 Connection | Function |
|---|---|---|---|
| **VCC** | Power | **5V Power Rail** | Module power (requires 150–330 mA; power from 5V rail) [E5] |
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **DIN** | Serial Data In | **GPIO 23** (via 74HCT buffer) | VSPI MOSI serial data line [E4] |
| **CS** | Chip Select | **GPIO 27** (via 74HCT buffer) | Active-LOW chip select / load pulse [E5] |
| **CLK** | Serial Clock | **GPIO 18** (via 74HCT buffer) | VSPI SCK clock line [E4] |

## ESP32 Sample Code (Core 3.x) — Smiley Face Display

```cpp
#include <SPI.h>

const int csPin = 27; // Chip Select (Active LOW)

// 8x8 Bitmap of a Smiley Face
const byte smileFace[8] = {
  0b00111100,
  0b01000010,
  0b10100101,
  0b10000001,
  0b10100101,
  0b10011001,
  0b01000010,
  0b00111100
};

void max7219Write(byte reg, byte data) {
  digitalWrite(csPin, LOW);
  SPI.transfer(reg);
  SPI.transfer(data);
  digitalWrite(csPin, HIGH);
}

void initMAX7219() {
  max7219Write(0x09, 0x00); // Decode mode: none
  max7219Write(0x0A, 0x03); // Intensity: low (3/15)
  max7219Write(0x0B, 0x07); // Scan limit: all 8 digits
  max7219Write(0x0C, 0x01); // Normal operation (exit shutdown)
  max7219Write(0x0F, 0x00); // Display test: off
  
  // Clear display
  for (byte r = 1; r <= 8; r++) {
    max7219Write(r, 0x00);
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(csPin, OUTPUT);
  digitalWrite(csPin, HIGH);

  // Initialize VSPI bus (SCK=18, MISO=19, MOSI=23, SS=csPin)
  SPI.begin(18, 19, 23, csPin);
  SPI.setFrequency(10000000); // 10 MHz clock
  SPI.setDataMode(SPI_MODE0);
  SPI.setBitOrder(MSBFIRST);

  initMAX7219();
  Serial.println("ESP32 MAX7219 Matrix Initialized.");

  // Display Smiley
  for (byte r = 0; r < 8; r++) {
    max7219Write(r + 1, smileFace[r]);
  }
}

void loop() {
  delay(1000);
}
```

## Source References
- Maxim Integrated MAX7219 Datasheet: https://datasheets.maximintegrated.com/en/ds/MAX7219-MAX7221.pdf
- MD_MAX72xx Library by majicDesigns: https://github.com/MajicDesigns/MD_MAX72XX
