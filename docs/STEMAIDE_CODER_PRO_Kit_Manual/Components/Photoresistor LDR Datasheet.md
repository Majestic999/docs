# Photoresistor (LDR GL5528) — Technical Datasheet

## General Description

A light-dependent resistor (LDR) composed of a photosensitive cadmium sulfide (CdS) semiconductor film on an alumina substrate. The resistance of the LDR decreases non-linearly with increasing incident ambient light intensity (lux) [E1].

### Circuit Integration on the 3.3V ESP32 Platform
To read the LDR using the **Espressif ESP32-DevKitC V4**:
1. **Voltage Rail**: The resistor voltage divider must be powered strictly from the **`3V3` rail** (NOT 5V) to match the ESP32's 0–3.3V ADC dynamic range [E5].
2. **Dedicated ADC1 Mapping**: The analog voltage must be connected to **`GPIO 35` (ADC1_CH7)** to guarantee that analog readings function continuously during active Wi-Fi and Bluetooth transmission [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **LDR Model** | GL5528 (5 mm diameter CdS photoresistor) | [E1] |
| **Max Voltage Rating** | 150 V DC | [E1] |
| **Max Power Dissipation** | 100 mW (at 25 °C) | [E1] |
| **Spectral Peak** | 540 nm (matches human eye photopic sensitivity) | [E1] |
| **Light Resistance (10 Lux)** | 10 kΩ to 20 kΩ | [E1] |
| **Dark Resistance (0 Lux)** | 1.0 MΩ minimum | [E1] |
| **Operating Voltage Rail** | **3.3 V DC (from ESP32 3V3 Rail)** | [E5] |
| **Target ADC Pin** | **ESP32 GPIO 35 (ADC1 Channel 7)** | [E5] |

## Voltage Divider Circuit & Transfer Function

```
               3.3V Regulated Rail
                   │
                   ├───[ Photoresistor (LDR) ]
                   │            │
                   │            ├───► ESP32 GPIO 35 (ADC1_CH7)
                   │            │
                   ├───[ 10.0 kΩ Resistor ]
                   │            │
                  GND ──────────┴──── Common Ground
```

$$V_{\text{out}} = 3.3\text{ V} \times \left( \frac{10\text{ k}\Omega}{R_{\text{LDR}} + 10\text{ k}\Omega} \right) \quad [\text{E5}]$$
- In Bright Light ($R_{\text{LDR}} \approx 2\text{ k}\Omega$): $V_{\text{out}} = 3.3\text{V} \times \frac{10}{12} = 2.75\text{ V}$ (High ADC value) [E5].
- In Darkness ($R_{\text{LDR}} > 500\text{ k}\Omega$): $V_{\text{out}} < 0.06\text{ V}$ (Low ADC value) [E5].

## ESP32 Sample Code (Core 3.x) — Calibrated Light Meter

```cpp
const int ldrPin = 35; // Dedicated ADC1 pin (Wi-Fi safe)

void setup() {
  Serial.begin(115200);
  analogSetAttenuation(ADC_11db); // Full 0-3.3V scale
  Serial.println("ESP32 LDR Light Sensor Initialized.");
}

void loop() {
  uint32_t mv = analogReadMilliVolts(ldrPin);
  int raw = analogRead(ldrPin);

  Serial.printf("LDR Raw ADC: %d | Voltage: %u mV | Light Level: %s\n",
                raw, mv, (mv > 2000) ? "BRIGHT" : (mv > 800) ? "NORMAL" : "DARK");
  delay(500);
}
```

## Source References
- Senba Optical GL5528 CdS Photoresistor Datasheet: https://www.senbasensor.com
