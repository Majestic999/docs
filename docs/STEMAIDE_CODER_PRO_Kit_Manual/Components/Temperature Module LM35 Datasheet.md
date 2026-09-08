# Temperature Module LM35 — Technical Datasheet

## General Description

The LM35 is a precision analog integrated-circuit temperature sensor whose output voltage is linearly proportional to the Celsius (Centigrade) temperature. The sensor has an inherent scale factor of **+10.0 mV/°C** (e.g. 250 mV at 25 °C) and does not require external calibration [E1].

### Electrical & ADC Integration on the ESP32 Platform
Interfacing the LM35 with the **Espressif ESP32-DevKitC V4** requires addressing two specific physical constraints:
1. **Supply Voltage Requirement ($V_{S\min} = 4.0\text{V}$)**: The LM35 silicon specification requires a minimum supply voltage of $4.0\text{ V}$ [E1]. Therefore, the sensor **must be powered from the 5V power rail**, NOT the 3.3V rail! Its output voltage ($0\text{ to }1.0\text{ V}$ for $0\text{ to }100\text{ }^\circ\text{C}$) remains safely within the ESP32's 3.3V ADC input range [E1/E5].
2. **ADC Attenuation & Non-Linearity Mitigation**: At the default $11\text{ dB}$ attenuation ($0\text{–}3.3\text{V}$ range), the ESP32 ADC has a non-linear deadband below $100\text{–}150\text{ mV}$ ($0\text{–}15\text{ }^\circ\text{C}$ room temperature distortion) [E1/E4]. To measure room temperature accurately, software must configure **`analogSetAttenuation(ADC_0db)`** (covering $0\text{ to }1.1\text{ V}$) and use factory calibration (`analogReadMilliVolts()`) [E4/E5]. Connects to **`GPIO 33` (ADC1_CH5)** [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Sensor IC** | Texas Instruments LM35 Precision Centigrade Temperature Sensor | [E1] |
| **Scale Factor** | **+10.0 mV / °C** linear scale factor | [E1] |
| **Operating Supply Voltage** | **4.0 V to 30.0 V DC (Powered from 5V Power Rail)** | [E1/E5] |
| **Measurement Range** | 0 °C to +100 °C (Basic unipolar circuit configuration) | [E1] |
| **Measurement Accuracy** | ±0.5 °C accuracy (at +25 °C); ±0.75 °C over full range | [E1] |
| **Quiescent Current** | ~60 µA to 130 µA (Very low self-heating: < 0.08 °C in still air) | [E1] |
| **ESP32 Target ADC Pin** | **GPIO 33 (ADC1 Channel 5 — Wi-Fi Concurrent)** | [E5] |
| **ESP32 ADC Attenuation**| **`ADC_0db` (0 to 1.1 V Full Scale Range)** | [E4/E5] |

## Pinout & Interfacing Schematic

```
          LM35 (TO-92 Package)
                ┌─────────┐
                │  LM35   │
                │  Flat   │
                │  Front  │
                └─────────┘
                 │   │   │
                 1   2   3
                VCC VOUT GND
```

| Pin # | Label | ESP32 Connection | Description |
|---|---|---|---|
| 1 | **VCC** | **5V Power Rail** | Powered from 5V rail ($V_S \ge 4.0\text{V}$ required) [E1] |
| 2 | **VOUT**| **GPIO 33 (ADC1_CH5)** | Linear analog output (10 mV/°C) [E5] |
| 3 | **GND** | **GND** | Common ground reference [E1] |

```
   ESP32-DevKitC V4                      LM35 Temperature Sensor
  ┌────────────────┐                     ┌──────────────────────┐
  │             5V ├────────────────────►│ VCC (Pin 1)          │
  │        GPIO 33 ├◄────────────────────┤ VOUT (Pin 2)         │
  │            GND ├─────────────────────┤ GND (Pin 3)          │
  └────────────────┘                     └──────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Precision Calibrated 0 dB Reading

```cpp
const int lm35Pin = 33; // ADC1 Input Pin

void setup() {
  Serial.begin(115200);

  // Set attenuation to 0 dB covering 0 to ~1100 mV (0 to 110 °C)
  analogSetAttenuation(ADC_0db);

  Serial.println("ESP32 LM35 Temperature Sensor (0 dB Attenuation) Ready.");
}

void loop() {
  // Read factory-calibrated millivolts
  uint32_t millivolts = analogReadMilliVolts(lm35Pin);

  // Temperature = Millivolts / 10.0 mV/°C
  float temperatureC = millivolts / 10.0;
  float temperatureF = (temperatureC * 9.0 / 5.0) + 32.0;

  Serial.printf("Output: %4u mV | Temp: %5.1f °C | %5.1f °F\n",
                millivolts, temperatureC, temperatureF);
  delay(1000);
}
```

## Source References
- Texas Instruments LM35 Precision Centigrade Temperature Sensors Datasheet: https://www.ti.com/lit/ds/symlink/lm35.pdf
