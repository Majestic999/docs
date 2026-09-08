# Potentiometer 10kΩ — Technical Datasheet

## General Description

A 3-terminal rotary carbon-film potentiometer with a linear (Type B) taper. Rotating the central shaft moves an internal wiper contact along a resistive track, establishing an adjustable voltage divider between the two outer terminals [E1].

### 3.3V Wiring Mandate for the ESP32 Platform
> [!CAUTION]
> **Strict 3.3V Terminal Connection**:
> The two outer fixed terminals of the potentiometer must connect strictly between **`3V3` and `GND`** (NOT 5V!). Connecting the outer terminal to 5V will cause the wiper to output voltages up to 5.0V, permanently damaging the ESP32 ADC pin [E1/E5]!

The wiper connects directly to **`GPIO 34` (ADC1_CH6)**, an input-only pin dedicated to analog signals [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Nominal Resistance** | 10 kΩ ±20% | [E1] |
| **Resistance Taper** | Linear (Type B) | [E1] |
| **Rotational Angle** | 300° ±5° mechanical rotation | [E1] |
| **Power Rating** | 0.1 W (100 mW) | [E1] |
| **Max Operating Voltage** | 50 V AC / 20 V DC | [E1] |
| **Rotational Life** | $\ge 15,000$ cycles | [E1] |
| **Supply Voltage in Kit** | **3.3 V DC (from ESP32 3V3 Rail)** | [E5] |
| **Current Consumption** | $I = \frac{3.3\text{V}}{10\text{ k}\Omega} = 0.33\text{ mA}$ | [E5] |

## Wiring Schematic

```
   ESP32-DevKitC V4                      10kΩ Rotary Potentiometer
  ┌────────────────┐                     ┌────────────────────────┐
  │            3V3 ├────────────────────►│ Terminal 1 (Outer)     │
  │        GPIO 34 ├◄────────────────────┤ Terminal 2 (Wiper)     │
  │            GND ├─────────────────────┤ Terminal 3 (Outer)     │
  └────────────────┘                     └────────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Smooth Analog Input

```cpp
const int potPin = 34; // ADC1 Input-Only Pin

void setup() {
  Serial.begin(115200);
  analogSetAttenuation(ADC_11db); // 0-3.3V full dynamic range
  Serial.println("ESP32 Potentiometer Interface Initialized.");
}

void loop() {
  // Read calibrated millivolts
  uint32_t mv = analogReadMilliVolts(potPin);
  int raw = analogRead(potPin);

  // Compute percentage (0 to 100%)
  float percent = (raw / 4095.0) * 100.0;

  Serial.printf("Raw ADC: %4d | Calibrated: %4u mV | Dial: %5.1f %%\n",
                raw, mv, percent);
  delay(100);
}
```

## Source References
- Bourns Rotary Potentiometer Series Datasheet: https://www.bourns.com
