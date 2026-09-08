# Flame Sensor Module — Technical Datasheet

## General Description

An infrared-sensitive optical flame detection sensor based on a high-speed NPN phototransistor and an LM393 dual comparator. The phototransistor is sensitive to radiation in the infrared spectral range of 760 nm to 1100 nm (the dominant emission spectrum of open hydrocarbon flames) [E1]. 

The module features dual outputs: an analog output (AO) providing raw relative optical intensity, and a digital output (DO) triggered when infrared intensity crosses an adjustable threshold set by an onboard multi-turn potentiometer [E1].

### Native 3.3V Compatibility with ESP32
When interfacing with the **Espressif ESP32-DevKitC V4**, powering the module from the **`3V3` power rail** ensures that the comparator and phototransistor output strictly within the 0–3.3V LVTTL and ADC range [E1/E5]:
- **Analog Output (AO)**: Connects directly to **`GPIO 34` (ADC1)**, guaranteeing continuous analog conversion during active Wi-Fi operation [E5].
- **Digital Output (DO)**: Connects directly to **`GPIO 13`** for digital threshold detection and hardware interrupts [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Comparator IC** | Texas Instruments LM393 Dual Differential Comparator | [E1] |
| **Optical Receiver** | YG1006 / PT334-6C Infrared Phototransistor | [E1] |
| **Peak Detection Wavelength**| 760 nm to 1100 nm (Infrared flame spectrum) | [E1] |
| **Detection Angle** | ~60° conical field of view | [E1] |
| **Detection Distance** | Up to 100 cm (for standard candle / lighter flame) | [E1] |
| **Operating Voltage ($V_{CC}$)** | 3.3 V to 5.0 V DC (**Operate at 3.3 V on ESP32**) | [E1/E5] |
| **Current Consumption** | ~15 mA (including onboard status LEDs) | [E1] |
| **Outputs** | Analog Output (AO) and Digital Comparator Output (DO) | [E1] |
| **Logic Level** | **3.3 V LVTTL** (when powered from 3.3V rail) | [E5] |

## Pinout & Wiring

| Pin Label | Function | ESP32-DevKitC V4 Connection | Description |
|---|---|---|---|
| **VCC** | Power Supply | **3V3 Rail** | Connect to 3.3V regulated rail [E5] |
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **DO** | Digital Output | **GPIO 13** | Digital LOW when flame detected (Active LOW) [E5] |
| **AO** | Analog Output | **GPIO 34** | Analog voltage inversely proportional to IR intensity [E5] |

```
   ESP32-DevKitC V4                      Flame Sensor Module
  ┌────────────────┐                     ┌───────────────────┐
  │            3V3 ├────────────────────►│ VCC (3.3V)        │
  │        GPIO 34 ├◄────────────────────┤ AO (ADC1_CH6)     │
  │        GPIO 13 ├◄────────────────────┤ DO (Digital Out)  │
  │            GND ├─────────────────────┤ GND               │
  └────────────────┘                     └───────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Flame Alarm Monitor

```cpp
const int flameAnalogPin = 34;  // Dedicated ADC1 pin
const int flameDigitalPin = 13; // Digital comparator output

void setup() {
  Serial.begin(115200);
  pinMode(flameDigitalPin, INPUT);
  analogSetAttenuation(ADC_11db); // Full 0-3.3V scale
  Serial.println("ESP32 Flame Sensor Initialized.");
}

void loop() {
  // Read digital threshold
  int flameDetected = digitalRead(flameDigitalPin);

  // Read calibrated analog intensity
  uint32_t mv = analogReadMilliVolts(flameAnalogPin);
  int rawADC = analogRead(flameAnalogPin);

  if (flameDetected == LOW) { // LM393 pulls LOW on detection
    Serial.printf(">>> FIRE DETECTED! | Analog Level: %d (%u mV)\n", rawADC, mv);
  } else {
    Serial.printf("--- Normal Monitoring | Analog Level: %d (%u mV)\n", rawADC, mv);
  }
  delay(500);
}
```

## Source References
- Texas Instruments LM393 Dual Comparator Datasheet: https://www.ti.com/lit/ds/symlink/lm393.pdf
- Everlight Infrared Phototransistor Specifications: https://www.everlight.com
