# 4-Digit 7-Segment Display Tube — Technical Datasheet

## General Description

A 4-digit 7-segment LED display tube with colon or decimal points. The display utilizes a multiplexed common cathode (CC) or common anode (CA) architecture where identical segment anodes (a–g, dp) across all four digits are tied together internally, while each digit has its own common cathode pin (DIG1–DIG4) [E1].

This multiplexed design requires 12 pins total (8 segments + 4 digit commons). When interfacing with the **Espressif ESP32-DevKitC V4**, time-division multiplexing is executed at $\ge 60\text{ Hz}$ per digit (240 Hz total refresh) to prevent visible flicker while keeping GPIO current within recommended ratings ($\le 12	ext{ mA}$) [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Display Configuration** | 4 digits, 7 segments per digit with decimal points / colon | [E1] |
| **Multiplexing Format** | Common Cathode (CC typical) or Common Anode (CA) | [E1] |
| **Colour** | Red (~630 nm) | [E1] |
| **Forward Voltage ($V_F$)** | 1.8 V to 2.1 V per segment | [E1] |
| **Continuous Forward Current** | 20 mA per segment max | [E1] |
| **Recommended Peak Pulse Current** | 10 mA to 15 mA per segment during multiplexed pulse | [E5] |
| **Digit Height** | 0.36" (9.2 mm) or 0.56" (14.2 mm) | [E1] |
| **Pin Count** | 12 pins (2 rows of 6 pins, 0.1" pitch) | [E1] |
| **Refresh Frequency** | $\ge 60\text{ Hz}$ per digit ($\ge 240\text{ Hz}$ complete frame rate) | [E5] |
| **Logic Compatibility** | **3.3 V LVTTL** with series resistors on segment lines | [E5] |

## Pinout (Standard 12-Pin DIP Package)

```
        Pin 12   Pin 11   Pin 10   Pin 9    Pin 8    Pin 7
        (DIG1)    (a)      (f)     (DIG2)   (DIG3)    (b)
       ┌──────────────────────────────────────────────────┐
       │   [ 8. ]       [ 8. ]       [ 8. ]       [ 8. ]  │
       │   DIG 1        DIG 2        DIG 3        DIG 4   │
       └──────────────────────────────────────────────────┘
         (e)      (d)      (dp)     (c)      (g)    (DIG4)
        Pin 1    Pin 2    Pin 3    Pin 4    Pin 5    Pin 6
```

| Pin # | Symbol | ESP32 GPIO Connection | Function & Electrical Role |
|---|---|---|---|
| 1 | **e** | **GPIO 25** (via 220 Ω) | Segment E anode drive [E5] |
| 2 | **d** | **GPIO 26** (via 220 Ω) | Segment D anode drive [E5] |
| 3 | **dp** | **GPIO 14** (via 220 Ω) | Decimal point anode drive [E5] |
| 4 | **c** | **GPIO 27** (via 220 Ω) | Segment C anode drive [E5] |
| 5 | **g** | **GPIO 33** (via 220 Ω) | Segment G anode drive [E5] |
| 6 | **DIG4**| **GPIO 17** | Digit 4 Common Cathode selector (Active LOW) [E5] |
| 7 | **b** | **GPIO 13** (via 220 Ω) | Segment B anode drive [E5] |
| 8 | **DIG3**| **GPIO 16** | Digit 3 Common Cathode selector (Active LOW) [E5] |
| 9 | **DIG2**| **GPIO 4** | Digit 2 Common Cathode selector (Active LOW) [E5] |
| 10 | **f** | **GPIO 32** (via 220 Ω) | Segment F anode drive [E5] |
| 11 | **a** | **GPIO 23** (via 220 Ω) | Segment A anode drive [E5] |
| 12 | **DIG1**| **GPIO 18** | Digit 1 Common Cathode selector (Active LOW) [E5] |

> [!TIP]
> **Hardware Driver Option: 74HC595 / MAX7219**:
> While direct multiplexing works well for isolated labs, capstone builds should use the kit's **74HC595 shift register** or **MAX7219 driver** to free up 9 GPIOs for sensors and radios [E5].

## ESP32 Sample Code (Core 3.x) — High-Speed Multiplexed Counter

```cpp
// Segment pins (a, b, c, d, e, f, g, dp)
const int segPins[8] = {23, 13, 27, 26, 25, 32, 33, 14};

// Digit select pins (DIG1, DIG2, DIG3, DIG4) - Active LOW for Common Cathode
const int digPins[4] = {18, 4, 16, 17};

// 7-segment digit bitmasks {a, b, c, d, e, f, g, dp}
const byte digitTable[10] = {
  0b00111111, // 0
  0b00000110, // 1
  0b01011011, // 2
  0b01001111, // 3
  0b01100110, // 4
  0b01101101, // 5
  0b01111101, // 6
  0b00000111, // 7
  0b01111111, // 8
  0b01101111  // 9
};

volatile int displayValue = 1234;

void setSegments(byte mask) {
  for (int i = 0; i < 8; i++) {
    digitalWrite(segPins[i], (mask >> i) & 0x01);
  }
}

void displayMux(int value) {
  int digits[4];
  digits[0] = (value / 1000) % 10;
  digits[1] = (value / 100) % 10;
  digits[2] = (value / 10) % 10;
  digits[3] = value % 10;

  for (int d = 0; d < 4; d++) {
    // Disable all digits
    for (int i = 0; i < 4; i++) digitalWrite(digPins[i], HIGH);

    // Set segment patterns
    setSegments(digitTable[digits[d]]);

    // Enable current digit
    digitalWrite(digPins[d], LOW);
    delayMicroseconds(2000); // 2 ms per digit = 125 Hz frame rate
  }
}

void setup() {
  Serial.begin(115200);
  for (int i = 0; i < 8; i++) {
    pinMode(segPins[i], OUTPUT);
    digitalWrite(segPins[i], LOW);
  }
  for (int i = 0; i < 4; i++) {
    pinMode(digPins[i], OUTPUT);
    digitalWrite(digPins[i], HIGH);
  }
  Serial.println("ESP32 4-Digit Display Multiplexer Initialized.");
}

void loop() {
  static unsigned long lastTick = 0;
  if (millis() - lastTick >= 100) {
    lastTick = millis();
    displayValue = (displayValue + 1) % 10000;
  }
  displayMux(displayValue);
}
```

## Source References
- Lite-On Optoelectronics 4-Digit Displays: https://optoelectronics.liteon.com
- SparkFun 4-Digit 7-Segment Display Guide: https://learn.sparkfun.com
