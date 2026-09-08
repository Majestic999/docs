# Sound Sensor Module (KY-038) — Technical Datasheet

## General Description

An acoustic sound threshold detection sensor based on a high-sensitivity capacitive electret microphone and an LM393 dual differential comparator. The module provides both an analog output (AO) representing the amplified acoustic soundwave and a digital output (DO) that triggers when sound levels exceed an adjustable threshold set by an onboard multi-turn trimmer [E1].

### Native 3.3V Integration on the ESP32 Platform
When interfacing with the **Espressif ESP32-DevKitC V4**, powering the module from the **`3V3` power rail** ensures that both the analog and digital comparator outputs remain strictly within the 0–3.3V range [E1/E5]:
- **Analog Output (AO)**: Connects to **`GPIO 35` (ADC1_CH7)**, ensuring analog audio sampling remains operational during active Wi-Fi transmission [E5].
- **Digital Output (DO)**: Connects to **`GPIO 14`** for sound threshold detection and hardware clap/whistle interrupts [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Microphone Type** | High-sensitivity omnidirectional electret condenser microphone | [E1] |
| **Comparator IC** | Texas Instruments LM393 Dual Comparator | [E1] |
| **Frequency Range** | 50 Hz to 10 kHz (audible voice and clapping range) | [E1] |
| **Operating Voltage ($V_{CC}$)** | 3.3 V to 5.0 V DC (**Operate at 3.3 V on ESP32**) | [E1/E5] |
| **Quiescent Current** | ~4 mA to 6 mA | [E1] |
| **Outputs** | Analog Sound Output (AO) and Digital Comparator Output (DO) | [E1] |
| **Logic Compatibility** | **3.3 V LVTTL** (Direct connection when powered at 3.3V) | [E5] |

## Pinout & Wiring

| Pin Label | Function | ESP32-DevKitC V4 Connection | Notes |
|---|---|---|---|
| **AO** | Analog Output | **GPIO 35 (ADC1_CH7)** | Analog audio waveform (Wi-Fi concurrent) [E5] |
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **VCC** | Power Supply | **3V3 Rail** | Connect to 3.3V regulated rail [E5] |
| **DO** | Digital Output | **GPIO 14** | Active-LOW comparator output on loud sound [E5] |

```
   ESP32-DevKitC V4                      Sound Sensor (KY-038)
  ┌────────────────┐                     ┌────────────────────┐
  │            3V3 ├────────────────────►│ VCC (3.3V)         │
  │        GPIO 35 ├◄────────────────────┤ AO (ADC1_CH7)      │
  │        GPIO 14 ├◄────────────────────┤ DO (Digital Out)   │
  │            GND ├─────────────────────┤ GND                │
  └────────────────┘                     └────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Clap Detector & Noise Meter

```cpp
const int soundAnalogPin = 35;  // ADC1 Input
const int soundDigitalPin = 14; // Comparator Digital Input

void setup() {
  Serial.begin(115200);
  pinMode(soundDigitalPin, INPUT);
  analogSetAttenuation(ADC_11db); // 0-3.3V scale
  Serial.println("ESP32 Sound Sensor Initialized.");
}

void loop() {
  int thresholdTriggered = digitalRead(soundDigitalPin);
  uint32_t mv = analogReadMilliVolts(soundAnalogPin);
  int raw = analogRead(soundAnalogPin);

  if (thresholdTriggered == LOW) { // LM393 pulls LOW on threshold exceed
    Serial.printf(">>> CLAP / NOISE DETECTED! | Level: %u mV (Raw: %d)\n", mv, raw);
    delay(200); // Debounce clap
  }

  static unsigned long lastLog = 0;
  if (millis() - lastLog >= 500) {
    lastLog = millis();
    Serial.printf("Ambient Sound Level: %u mV (Raw: %d)\n", mv, raw);
  }
}
```

## Source References
- Texas Instruments LM393 Technical Datasheet: https://www.ti.com
