# Water Test Module (Raindrop / Level Sensor) — Technical Datasheet

## General Description

A resistive liquid level and raindrop detection sensor featuring parallel interdigitated printed copper traces on an FR-4 PCB. When water droplets or submersion bridges the traces, electrical conductance increases proportionally with liquid surface coverage, forming an active variable resistor [E1].

### 3.3V Power & ADC1 Mapping on the ESP32 Platform
1. **Powering at 3.3V to Prevent Galvanic Corrosion**: While originally tested at 5V on Arduino UNO R3, DC excitation of submerged copper traces causes rapid electrolytic oxidation (galvanic corrosion). Powering the sensor from the **ESP32 `3V3` rail** reduces electrolytic stripping and natively scales the analog output voltage between $0\text{ and }3.3\text{ V}$ [E1/E5].
2. **Dedicated ADC1 Mapping**: The analog output connects to **`GPIO 32` (ADC1_CH4)**, ensuring continuous water monitoring during active Wi-Fi data reporting [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Sensor Type** | Resistive track water level / raindrop sensor | [E1] |
| **Operating Voltage ($V_{CC}$)** | 3.3 V to 5.0 V DC (**Operated at 3.3 V on ESP32**) | [E1/E5] |
| **Current Consumption** | < 20 mA (active submersion); 0 mA (dry) | [E1] |
| **Effective Sensor Area** | 40 × 16 mm (interdigitated trace grid) | [E1] |
| **Output Type** | Analog voltage (Increases with water level) | [E1] |
| **Target ADC Pin** | **ESP32 GPIO 32 (ADC1 Channel 4 — Wi-Fi Concurrent)** | [E5] |

## Pinout & Wiring

| Pin Label | Function | ESP32-DevKitC V4 Connection | Description |
|---|---|---|---|
| **+ (VCC)** | Power Supply | **3V3 Rail** | 3.3V DC power (reduces electrode corrosion) [E5] |
| **− (GND)** | Ground | **GND** | Common ground reference [E1] |
| **S (Signal)**| Analog Output | **GPIO 32 (ADC1_CH4)** | Voltage increases as water level rises [E5] |

```
   ESP32-DevKitC V4                      Water Test Module
  ┌────────────────┐                     ┌─────────────────┐
  │            3V3 ├────────────────────►│ + (VCC)         │
  │        GPIO 32 ├◄────────────────────┤ S (Signal)      │
  │            GND ├─────────────────────┤ − (GND)         │
  └────────────────┘                     └─────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Water Level Monitor

```cpp
const int waterSensorPin = 32; // Dedicated ADC1 pin

void setup() {
  Serial.begin(115200);
  analogSetAttenuation(ADC_11db); // 0-3.3V scale
  Serial.println("ESP32 Water Level Sensor Initialized.");
}

void loop() {
  uint32_t mv = analogReadMilliVolts(waterSensorPin);
  int rawADC = analogRead(waterSensorPin);

  const char* status = "DRY";
  if (mv > 1800) status = "HIGH SUBMERSION";
  else if (mv > 800) status = "MEDIUM WATER";
  else if (mv > 200) status = "DROPLETS DETECTED";

  Serial.printf("Raw ADC: %4d | Voltage: %4u mV | Status: %s\n", rawADC, mv, status);
  delay(1000);
}
```

## Source References
- DFRobot Liquid Level Sensor Technical Reference: https://wiki.dfrobot.com
