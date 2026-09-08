# 4x4 Matrix Keypad — Technical Datasheet

## General Description

A 16-button passive tactile matrix keypad arranged into 4 rows and 4 columns. Pressing a key connects a specific row conductor to a column conductor. By scanning rows and sensing column states, 16 distinct inputs are evaluated using only 8 microcontroller GPIOs [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, rows and columns must be mapped strictly to bidirectional GPIOs supporting internal pull-up resistors (`INPUT_PULLUP`). **Input-only pins (`GPIO 34–39`) lack internal pull-up/pull-down resistors and cannot be used for column sensing without external physical pull-up resistors** [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Key Array** | 16 momentary switches (0–9, A–D, *, #) | [E1] |
| **Switch Type** | Passive membrane or tactile mechanical switches | [E1] |
| **Max Contact Voltage** | 35 V DC | [E1] |
| **Max Contact Current** | 100 mA | [E1] |
| **Contact Resistance** | 10 Ω to 500 Ω (typical membrane trace resistance) | [E1] |
| **Dielectric Withstand**| 250 V RMS | [E1] |
| **Bounce Time** | $\le 5\text{ ms}$ (software debounce recommended: 10–20 ms) | [E1/E4] |
| **Connector** | 8-pin 0.1" (2.54 mm) female header | [E1] |
| **Logic Compatibility** | **3.3 V LVTTL** using ESP32 internal pull-ups | [E5] |

## Pinout & ESP32 Connection

```
           4x4 Matrix Keypad Pinout
       ┌───┬───┬───┬───┬───┬───┬───┬───┐
       │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │ 8 │
       └───┴───┴───┴───┴───┴───┴───┴───┘
         R1  R2  R3  R4  C1  C2  C3  C4
```

| Pin # | Matrix Line | ESP32 GPIO Connection | Pin Capability & Direction | Notes |
|---|---|---|---|---|
| 1 | **Row 1 (R1)** | **GPIO 13** | Bidirectional Output | Driven LOW sequentially during row scanning [E5] |
| 2 | **Row 2 (R2)** | **GPIO 14** | Bidirectional Output | Driven LOW sequentially during row scanning [E5] |
| 3 | **Row 3 (R3)** | **GPIO 25** | Bidirectional Output | Driven LOW sequentially during row scanning [E5] |
| 4 | **Row 4 (R4)** | **GPIO 26** | Bidirectional Output | Driven LOW sequentially during row scanning [E5] |
| 5 | **Col 1 (C1)** | **GPIO 27** | Bidirectional Input (`INPUT_PULLUP`) | Sensed LOW when key pressed [E5] |
| 6 | **Col 2 (C2)** | **GPIO 32** | Bidirectional Input (`INPUT_PULLUP`) | Sensed LOW when key pressed [E5] |
| 7 | **Col 3 (C3)** | **GPIO 33** | Bidirectional Input (`INPUT_PULLUP`) | Sensed LOW when key pressed [E5] |
| 8 | **Col 4 (C4)** | **GPIO 4** | Bidirectional Input (`INPUT_PULLUP`) | Sensed LOW when key pressed [E5] |

## ESP32 Sample Code (Core 3.x) — Keypad Scanning

```cpp
#include <Keypad.h>

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};

// Connect to bidirectional ESP32 pins
byte rowPins[ROWS] = {13, 14, 25, 26}; 
byte colPins[COLS] = {27, 32, 33, 4};  

Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

void setup() {
  Serial.begin(115200);
  Serial.println("ESP32 4x4 Matrix Keypad Initialized.");
}

void loop() {
  char key = keypad.getKey();
  if (key) {
    Serial.print("Key Pressed: ");
    Serial.println(key);
  }
}
```

## Source References
- Grayhill Keypad Design Guide: https://www.grayhill.com
- Arduino Keypad Library Repository: https://github.com/Chris--A/Keypad
