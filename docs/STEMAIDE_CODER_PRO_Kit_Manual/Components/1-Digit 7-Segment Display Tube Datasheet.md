# 1-Digit 7-Segment Display Tube — Technical Datasheet

## General Description

A single-digit 7-segment LED display with decimal point. Each segment is an individual LED with a forward voltage of ~1.8V to 2.0V (red) [E1]. Available in common cathode (CC) or common anode (CA) configuration. Used in the STEMAIDE Coder Pro Kit for numeric readout, countdown timers, and sensor threshold indicators [E5]. 

When interfaced with the **Espressif ESP32-DevKitC V4 (3.3V logic)**, current-limiting series resistors (150 Ω to 220 Ω) are required on each segment line to limit current to safe levels (5.9 mA to 8.7 mA per GPIO), ensuring full compliance with the ESP32 recommended continuous current limit of $\le 12\text{ mA}$ per pin [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Display Type** | 1-digit 7-segment LED display with decimal point | [E1] |
| **Configuration** | Common Cathode (CC) or Common Anode (CA) | [E1] |
| **Emitted Colour** | Red (typical wavelength ~630–650 nm) | [E1] |
| **Forward Voltage ($V_F$)** | ~1.8 V to 2.0 V per segment | [E1] |
| **Max Forward Current ($I_F$)** | 20 mA per segment (Continuous limit) | [E1] |
| **Recommended Drive Current** | 6 mA to 10 mA (bright, high-efficiency output at 3.3V) | [E5] |
| **Digit Height** | 0.56" (14.2 mm) or 0.36" (9.14 mm) | [E1] |
| **Luminous Intensity** | 15–60 mcd per segment | [E1] |
| **Pin Count** | 10 pins (8 segment anodes/cathodes + 2 common pins) | [E1] |
| **Lead Pitch** | 2.54 mm (0.1") — Breadboard compatible | [E1] |
| **Operating Logic Level** | **3.3 V LVTTL** (Direct drive via series resistors or 74HC595) | [E5] |

## Segment Labelling

```
      ┌─── a ───┐
    f │         │ b
      ├─── g ───┤
    e │         │ c
      └─── d ───┘   ● dp

      SEGMENTS: a, b, c, d, e, f, g, dp
```

## Pinout & ESP32 Connection

| Pin # | Segment | ESP32 Isolated Lab Pin | ESP32 Function | Notes |
|---|---|---|---|---|
| 1 | **e** | **GPIO 25** | Digital Output | Connect through 220 Ω resistor [E5] |
| 2 | **d** | **GPIO 26** | Digital Output | Connect through 220 Ω resistor [E5] |
| 3 | **Common** | **GND** (CC) or **3V3** (CA) | Power Rail | Common Cathode: connect to GND [E5] |
| 4 | **c** | **GPIO 27** | Digital Output | Connect through 220 Ω resistor [E5] |
| 5 | **dp** | **GPIO 14** | Digital Output | Connect through 220 Ω resistor [E5] |
| 6 | **b** | **GPIO 13** | Digital Output | Connect through 220 Ω resistor [E5] |
| 7 | **a** | **GPIO 4** | Digital Output | Connect through 220 Ω resistor [E5] |
| 8 | **Common** | **GND** (CC) or **3V3** (CA) | Power Rail | Connected internally to pin 3 [E1] |
| 9 | **f** | **GPIO 32** | Digital Output | Connect through 220 Ω resistor [E5] |
| 10 | **g** | **GPIO 33** | Digital Output | Connect through 220 Ω resistor [E5] |

> [!NOTE]
> **Shift Register Scaling Option**:
> For projects with multiple sensors, driving the 7-segment display via the kit's **74HC595 shift register** reduces microcontroller pin consumption from 8 GPIOs down to just 3 SPI lines (`GPIO 23, 18, 4`) [E5].

## Wiring Diagram — Common Cathode (CC) with 220 Ω Resistors

Current calculation at 3.3V drive [E5]:
$$I = \frac{V_{\text{GPIO}} - V_F}{R} = \frac{3.3\text{ V} - 2.0\text{ V}}{220\,\Omega} = 5.91\text{ mA}$$

```
     ESP32-DevKitC V4                     7-Segment Display (CC)
    ┌────────────────┐                   ┌───────────────────────┐
    │        GPIO 4  ├───[ 220 Ω ]──────►│ a   (Pin 7)           │
    │        GPIO 13 ├───[ 220 Ω ]──────►│ b   (Pin 6)           │
    │        GPIO 27 ├───[ 220 Ω ]──────►│ c   (Pin 4)           │
    │        GPIO 26 ├───[ 220 Ω ]──────►│ d   (Pin 2)           │
    │        GPIO 25 ├───[ 220 Ω ]──────►│ e   (Pin 1)           │
    │        GPIO 32 ├───[ 220 Ω ]──────►│ f   (Pin 9)           │
    │        GPIO 33 ├───[ 220 Ω ]──────►│ g   (Pin 10)          │
    │        GPIO 14 ├───[ 220 Ω ]──────►│ dp  (Pin 5)           │
    │           GND  ├───────────────────┤ Common Cathode (3, 8) │
    └────────────────┘                   └───────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — 0 to 9 Counter

```cpp
// Segment pins: a, b, c, d, e, f, g, dp
const int segPins[8] = {4, 13, 27, 26, 25, 32, 33, 14};

// Digit patterns for Common Cathode (1 = ON, 0 = OFF)
// Segments ordered: {a, b, c, d, e, f, g, dp}
const byte digitPatterns[10][8] = {
  {1, 1, 1, 1, 1, 1, 0, 0}, // 0
  {0, 1, 1, 0, 0, 0, 0, 0}, // 1
  {1, 1, 0, 1, 1, 0, 1, 0}, // 2
  {1, 1, 1, 1, 0, 0, 1, 0}, // 3
  {0, 1, 1, 0, 0, 1, 1, 0}, // 4
  {1, 0, 1, 1, 0, 1, 1, 0}, // 5
  {1, 0, 1, 1, 1, 1, 1, 0}, // 6
  {1, 1, 1, 0, 0, 0, 0, 0}, // 7
  {1, 1, 1, 1, 1, 1, 1, 0}, // 8
  {1, 1, 1, 1, 0, 1, 1, 0}  // 9
};

void displayDigit(int num) {
  if (num < 0 || num > 9) return;
  for (int i = 0; i < 8; i++) {
    digitalWrite(segPins[i], digitPatterns[num][i]);
  }
}

void setup() {
  Serial.begin(115200);
  for (int i = 0; i < 8; i++) {
    pinMode(segPins[i], OUTPUT);
    digitalWrite(segPins[i], LOW);
  }
  Serial.println("ESP32 1-Digit 7-Segment Display Initialized.");
}

void loop() {
  for (int digit = 0; digit <= 9; digit++) {
    displayDigit(digit);
    delay(1000);
  }
}
```

## Source References
- Vishay Semiconductors 7-Segment Display Series: https://www.vishay.com/displays/
- Kingbright 7-Segment Displays: https://www.kingbrightusa.com
