# 5mm LED (Assorted Colours) — Technical Datasheet

## General Description

Through-hole 5mm diffuse and clear light-emitting diodes in red, green, yellow, blue, and white. Used across the STEMAIDE Coder Pro Kit for digital status indication, optical signalling, and pulse-width modulated (PWM) fading [E1].

When driven directly by the **Espressif ESP32-DevKitC V4 (3.3V logic)**, current-limiting resistors must be recalculated to match the 3.3V rail. Driving LEDs directly without resistors will burn out the LED and destroy the ESP32 GPIO driver stage [E1/E5].

## Specifications by Colour

| Colour | Typical Forward Voltage ($V_F$) | Max Forward Current ($I_F$) | Recommended 3.3V Resistor | Actual Current ($I_F$) | Luminous Intensity | Evidence |
|---|---|---|---|---|---|---|
| **Red** | 1.8 V – 2.0 V | 20 mA | **220 Ω** | $\frac{3.3 - 1.9}{220} = 6.4\text{ mA}$ | 20–50 mcd | [E1/E5] |
| **Yellow** | 1.9 V – 2.1 V | 20 mA | **220 Ω** | $\frac{3.3 - 2.0}{220} = 5.9\text{ mA}$ | 20–50 mcd | [E1/E5] |
| **Green** | 2.1 V – 3.0 V (GaP/InGaN) | 20 mA | **150 Ω** | $\frac{3.3 - 2.2}{150} = 7.3\text{ mA}$ | 30–80 mcd | [E1/E5] |
| **Blue** | 3.0 V – 3.2 V (InGaN) | 20 mA | **100 Ω** | $\frac{3.3 - 3.1}{100} = 2.0\text{ mA}$ | 100–300 mcd | [E1/E5] |
| **White** | 3.0 V – 3.3 V (InGaN + Phosphor)| 20 mA | **100 Ω** | $\frac{3.3 - 3.1}{100} = 2.0\text{ mA}$ | 200–500 mcd | [E1/E5] |

> [!IMPORTANT]
> **ESP32 GPIO Current Limit Rule**:
> Recommended continuous source current per ESP32 GPIO is $\le 12\text{ mA}$ (nominal drive strength level 2) [E1]. The resistor values above guarantee high visibility while keeping total current safely below 8 mA [E5].

## Pinout & Polarity

- **Anode (+)**: Longer lead; connects to ESP32 GPIO via series current-limiting resistor.
- **Cathode (−)**: Shorter lead; flat edge on plastic lens; connects to common GND.

## ESP32 Sample Code (Core 3.x) — Hardware LEDC PWM Fading

```cpp
const int ledPin = 25;       // Safe general-purpose GPIO
const int pwmFreq = 5000;    // 5 kHz PWM frequency (glitch-free)
const int pwmResolution = 8; // 8-bit resolution (0-255 duty cycle)

void setup() {
  Serial.begin(115200);
  // ESP32 Arduino Core 3.x unified LEDC API
  ledcAttach(ledPin, pwmFreq, pwmResolution);
  Serial.println("ESP32 LEDC PWM Fading Initialized.");
}

void loop() {
  // Fade in
  for (int duty = 0; duty <= 255; duty++) {
    ledcWrite(ledPin, duty);
    delay(5);
  }
  // Fade out
  for (int duty = 255; duty >= 0; duty--) {
    ledcWrite(ledPin, duty);
    delay(5);
  }
}
```

## Source References
- Kingbright 5mm LED Series Datasheets: https://www.kingbrightusa.com
- Everlight Optoelectronics Through-Hole LEDs: https://www.everlight.com
