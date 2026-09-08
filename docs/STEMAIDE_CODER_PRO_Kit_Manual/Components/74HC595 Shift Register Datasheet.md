# 74HC595 8-Bit Shift Register — Technical Datasheet

## General Description

The 74HC595 is a high-speed 8-bit serial-in, parallel-out shift register with an internal storage register and 3-state output drivers. It allows an embedded microcontroller to expand 3 digital control lines (Data, Clock, Latch) into 8 independent digital outputs [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, the 74HC595 VCC pin is powered directly from the **3.3V power rail**. Powering the IC at 3.3V guarantees 100% CMOS logic compatibility with the ESP32's 3.3V LVTTL outputs without requiring level shifters [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Logic Family** | High-Speed CMOS (74HC Series) | [E1] |
| **Operating Voltage ($V_{CC}$)** | 2.0 V to 6.0 V DC (**Operated at 3.3 V DC in ESP32 Kit**) | [E1/E5] |
| **Input High Threshold ($V_{IH}$)** | $0.7 \times V_{CC} = 2.31\text{ V}$ (at $V_{CC} = 3.3\text{ V}$) | [E1/E5] |
| **Input Low Threshold ($V_{IL}$)** | $0.3 \times V_{CC} = 0.99\text{ V}$ (at $V_{CC} = 3.3\text{ V}$) | [E1/E5] |
| **Max Clock Frequency** | $\ge 25\text{ MHz}$ at 3.3V (up to 100 MHz in 74AHC variants) | [E1] |
| **Output Drive Current** | $\pm 6\text{ mA}$ per pin at 3.3V; $\pm 35\text{ mA}$ absolute maximum | [E1] |
| **Total Package Current** | $\le 70\text{ mA}$ total through VCC/GND pins | [E1] |
| **Package** | 16-pin Dual In-Line Package (DIP-16, 0.3" row spacing) | [E1] |

## Pinout & ESP32 Connection

```
                   74HC595 DIP-16 Pinout
                      ┌───────∪───────┐
          Output Q1 ──┤ 1          16 ├── VCC (Connect to ESP32 3V3)
          Output Q2 ──┤ 2          15 ├── Output Q0
          Output Q3 ──┤ 3          14 ├── DS / SER (Serial Data)
          Output Q4 ──┤ 4          13 ├── OE (Output Enable - GND)
          Output Q5 ──┤ 5          12 ├── STCP / RCLK (Latch Clock)
          Output Q6 ──┤ 6          11 ├── SHCP / SRCLK (Shift Clock)
          Output Q7 ──┤ 7          10 ├── MR / SRCLR (Master Reset - 3V3)
                GND ──┤ 8           9 ├── Q7S (Serial Out - Cascade)
                      └───────────────┘
```

| Pin # | Symbol | ESP32 GPIO Connection | Function & Wiring |
|---|---|---|---|
| 1–7, 15 | **Q0–Q7** | Output Loads (LEDs via resistors) | 8 parallel outputs [E1] |
| 8 | **GND** | **GND** | System Ground [E1] |
| 9 | **Q7S** | Cascade to next 74HC595 | Serial output for daisy-chaining [E1] |
| 10 | **MR / SRCLR**| **3V3 Rail** | Active-LOW Master Reset (tied permanently HIGH) [E1] |
| 11 | **SHCP / SRCLK**| **GPIO 18** (VSPI SCK or GPIO) | Shift register clock input [E5] |
| 12 | **STCP / RCLK** | **GPIO 4** (or GPIO 5 in lab) | Storage latch clock input [E5] |
| 13 | **OE** | **GND** | Active-LOW Output Enable (tied permanently LOW) [E1] |
| 14 | **DS / SER** | **GPIO 23** (VSPI MOSI or GPIO)| Serial data input line [E5] |
| 16 | **VCC** | **3V3 Rail** | 3.3V DC logic supply [E5] |

## ESP32 Sample Code (Core 3.x) — 8-Bit Binary LED Chaser

```cpp
const int dataPin = 23;   // DS / SER
const int clockPin = 18;  // SHCP / SRCLK
const int latchPin = 4;   // STCP / RCLK

void updateShiftRegister(byte pattern) {
  digitalWrite(latchPin, LOW);
  shiftOut(dataPin, clockPin, MSBFIRST, pattern);
  digitalWrite(latchPin, HIGH);
}

void setup() {
  Serial.begin(115200);
  pinMode(dataPin, OUTPUT);
  pinMode(clockPin, OUTPUT);
  pinMode(latchPin, OUTPUT);
  Serial.println("ESP32 74HC595 Shift Register Initialized.");
}

void loop() {
  // Chaser pattern
  for (int i = 0; i < 8; i++) {
    updateShiftRegister(1 << i);
    delay(100);
  }
  for (int i = 6; i >= 1; i--) {
    updateShiftRegister(1 << i);
    delay(100);
  }
}
```

## Source References
- Texas Instruments SN74HC595 Datasheet: https://www.ti.com/lit/ds/symlink/sn74hc595.pdf
- Nexperia 74HC595 Technical Overview: https://www.nexperia.com
