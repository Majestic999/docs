# Active Buzzer Module — Technical Datasheet

## General Description

An active piezoelectric or magnetic buzzer module with an integrated internal oscillation circuit. Applying a DC voltage produces a continuous, piercing single-tone sound (approximately 2.3 kHz to 2.5 kHz) without requiring an external audio oscillation signal [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, driving the buzzer directly from a GPIO pin requires limiting current to $\le 12\text{ mA}$, or preferably driving the buzzer through a **2N2222 NPN transistor buffer** from the 5V power rail to maximize sound volume and protect the ESP32 GPIO [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Buzzer Type** | Active (internal oscillator built-in) | [E1] |
| **Operating Voltage** | 3.3 V to 5.0 V DC | [E1] |
| **Rated Voltage** | 5.0 V DC | [E1] |
| **Resonant Frequency** | 2300 Hz ± 300 Hz (2.3 kHz) | [E1] |
| **Current Consumption** | 20 mA to 30 mA at 5.0 V; ~10 mA at 3.3 V | [E1/E5] |
| **Sound Output Level (SPL)**| $\ge 85\text{ dB}$ at 10 cm (5V drive) | [E1] |
| **Operating Temperature** | −20 °C to +70 °C | [E1] |
| **Polarity** | Polarized: (+) Long pin / Red lead; (−) Short pin / Black lead | [E1] |

## Wiring Schematics

### Method 1: Transistor Driver Circuit (Recommended for Full 85 dB Volume)
```
               +5V Power Rail
                   │
                   ├───(+) [ Active Buzzer ] (−)
                   │             │
                   │             ▼ Collector
  ESP32 GPIO 26 ───┴───[ 1.0 kΩ ]───────►│  2N2222 NPN Transistor
                         (Base)          │ Emitter
                                         ├─── Common GND
```

### Method 2: Direct 3.3V Low-Current Drive
Connect buzzer (+) to `GPIO 26` through a series $100\,\Omega$ resistor, and buzzer (−) to `GND`. Current is limited to $\sim 10\text{ mA}$ [E5].

## ESP32 Sample Code (Core 3.x) — Alarm Beeper

```cpp
const int buzzerPin = 26; // Driven via 2N2222 transistor

void setup() {
  Serial.begin(115200);
  pinMode(buzzerPin, OUTPUT);
  digitalWrite(buzzerPin, LOW);
  Serial.println("ESP32 Active Buzzer Initialized.");
}

void loop() {
  // Beep-beep alarm pattern
  for (int i = 0; i < 3; i++) {
    digitalWrite(buzzerPin, HIGH); // Sound ON
    delay(100);
    digitalWrite(buzzerPin, LOW);  // Sound OFF
    delay(100);
  }
  delay(2000); // 2 second pause
}
```

## Source References
- CUI Devices Piezoelectric Buzzers Technical Guide: https://www.cuidevices.com
