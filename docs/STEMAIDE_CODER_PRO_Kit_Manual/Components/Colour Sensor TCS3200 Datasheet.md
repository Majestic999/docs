# Colour Sensor TCS3200 — Technical Datasheet

## General Description

The TCS3200 colour sensor combines silicon photodiodes and a current-to-frequency converter on a single monolithic CMOS integrated circuit. The photodiode array consists of a 4×4 grid of 16 photodiodes: 4 with red filters, 4 with green filters, 4 with blue filters, and 4 clear without filters [E1]. The output is a 50% duty cycle square wave whose frequency is directly proportional to light intensity [E1].

### Native 3.3V Operation on ESP32
The TCS3200 operates over an input supply voltage range of **2.7V to 5.5V DC** [E1]. When interfaced with the **Espressif ESP32-DevKitC V4**, powering the sensor from the **`3V3` rail** ensures that the frequency output pin (`OUT`) produces a **native 3.3V square wave**, eliminating the need for voltage dividers and allowing direct connection to ESP32 interrupt and pulse-counter pins [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Sensor IC** | AMS / TAOS TCS3200 | [E1] |
| **Operating Voltage ($V_{DD}$)** | 2.7 V to 5.5 V DC (**Operate at 3.3 V on ESP32**) | [E1/E5] |
| **Supply Current** | 2.0 mA typical (active); 15 µA (power-down) | [E1] |
| **Output Waveform** | 50% duty cycle square wave, frequency $\propto$ irradiance | [E1] |
| **Maximum Output Frequency** | Up to 500 kHz at 100% frequency scaling | [E1] |
| **Frequency Scaling (S0, S1)** | Power-down (00), 2% (01), 20% (10), 100% (11) | [E1] |
| **Filter Selection (S2, S3)** | Red (00), Blue (01), Clear (10), Green (11) | [E1] |
| **Onboard Illumination** | 4 white LEDs with current-limiting resistors | [E1] |
| **Logic Compatibility** | **3.3 V LVTTL Direct Drive** (when powered from 3.3V) | [E5] |

## Pinout & ESP32 Connection

| Pin Label | Function | ESP32 GPIO Connection | Description |
|---|---|---|---|
| **VCC** | Power Supply | **3V3 Rail** | 3.3V DC power (generates safe 3.3V output) [E5] |
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **OE** | Output Enable | **GND** | Active-LOW; tie to GND to permanently enable output [E1] |
| **OUT** | Frequency Output | **GPIO 14** | Connects to ESP32 hardware interrupt / pulse pin [E5] |
| **S0** | Frequency Scaling 0 | **GPIO 25** | Digital Output [E5] |
| **S1** | Frequency Scaling 1 | **GPIO 26** | Digital Output [E5] |
| **S2** | Filter Selection 0 | **GPIO 27** | Digital Output [E5] |
| **S3** | Filter Selection 1 | **GPIO 13** | Digital Output [E5] |

## Frequency Scaling & Filter Tables

```
 Frequency Scaling (S0, S1)          Photodiode Filter Selection (S2, S3)
  S0    S1    Scaling Ratio           S2    S3    Selected Colour Filter
  LOW   LOW   Power Down              LOW   LOW   Red Filter
  LOW   HIGH  2% (Recommended)        LOW   HIGH  Blue Filter
  HIGH  LOW   20%                     HIGH  LOW   Clear (Broadband / Luminance)
  HIGH  HIGH  100%                    HIGH  HIGH  Green Filter
```

## ESP32 Sample Code (Core 3.x) — RGB Colour Detection

```cpp
const int s0 = 25;
const int s1 = 26;
const int s2 = 27;
const int s3 = 13;
const int outPin = 14;

void setup() {
  Serial.begin(115200);
  pinMode(s0, OUTPUT);
  pinMode(s1, OUTPUT);
  pinMode(s2, OUTPUT);
  pinMode(s3, OUTPUT);
  pinMode(outPin, INPUT);

  // Set frequency scaling to 20% (High S0, Low S1)
  digitalWrite(s0, HIGH);
  digitalWrite(s1, LOW);

  Serial.println("ESP32 TCS3200 Colour Sensor Initialized.");
}

int readColor(bool s2Val, bool s3Val) {
  digitalWrite(s2, s2Val);
  digitalWrite(s3, s3Val);
  delayMicroseconds(100);
  // Measure half-period duration in microseconds
  return pulseIn(outPin, LOW, 50000);
}

void loop() {
  int redPulse = readColor(LOW, LOW);     // Red
  int bluePulse = readColor(LOW, HIGH);   // Blue
  int greenPulse = readColor(HIGH, HIGH); // Green

  Serial.printf("Raw Pulses -> R: %d us | G: %d us | B: %d us\n",
                redPulse, greenPulse, bluePulse);
  delay(500);
}
```

## Source References
- AMS / TAOS TCS3200 Programmable Color Light-to-Frequency Converter Datasheet: https://ams.com
