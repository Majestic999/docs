# DS18B20 Digital Temperature Sensor — Technical Datasheet

## General Description

The DS18B20 is a precision digital thermometer providing 9-bit to 12-bit Celsius temperature measurements over a proprietary 1-Wire serial bus. Each sensor possesses a unique 64-bit factory-lasered serial ROM code, allowing multiple sensors to operate on a single microcontroller data pin [E1].

### Native 3.3V Compatibility with ESP32
The DS18B20 operates over a supply voltage range of **3.0V to 5.5V DC** [E1]. When interfacing with the **Espressif ESP32-DevKitC V4**, powering the sensor from the **`3V3` rail** and connecting a **4.7 kΩ pull-up resistor to 3.3V** guarantees 100% native voltage matching on the 1-Wire bus (`GPIO 4`), completely eliminating overvoltage risk [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Sensor IC** | Maxim Integrated / Analog Devices DS18B20 | [E1] |
| **Operating Voltage ($V_{DD}$)** | 3.0 V to 5.5 V DC (**Operated at 3.3 V on ESP32**) | [E1/E5] |
| **Temperature Range** | −55 °C to +125 °C (−67 °F to +257 °F) | [E1] |
| **Measurement Accuracy** | ±0.5 °C accuracy from −10 °C to +85 °C | [E1] |
| **Selectable Resolution** | 9-bit (0.5 °C), 10-bit (0.25 °C), 11-bit (0.125 °C), 12-bit (0.0625 °C) | [E1] |
| **Conversion Time** | Max 750 ms (at 12-bit resolution); 93.75 ms (at 9-bit) | [E1] |
| **Standby Current** | 750 nA typical | [E1] |
| **Active Current** | 1.0 mA to 1.5 mA during temperature conversion | [E1] |
| **Bus Protocol** | Dallas / Maxim 1-Wire Serial Protocol | [E1] |
| **External Pull-up Resistor** | **4.7 kΩ** connected between DQ data line and 3.3V rail | [E1/E5] |

## Pinout & Wiring

```
          DS18B20 (TO-92 Package)
                ┌─────────┐
                │ DS18B20 │
                │  Flat   │
                │  Front  │
                └─────────┘
                 │   │   │
                 1   2   3
                GND  DQ VDD
```

| Pin # | Symbol | ESP32-DevKitC V4 Connection | Notes |
|---|---|---|---|
| 1 | **GND** | **GND** | Ground reference [E1] |
| 2 | **DQ** | **GPIO 4** (with 4.7 kΩ pull-up to 3V3) | 1-Wire bidirectional data bus [E5] |
| 3 | **VDD** | **3V3 Rail** | 3.3V regulated power [E5] |

```
   ESP32-DevKitC V4                      DS18B20 Sensor
  ┌────────────────┐                     ┌───────────────┐
  │            3V3 ├──────────┬─────────►│ VDD (Pin 3)   │
  │                │      [ 4.7 kΩ ]     │               │
  │                │          │          │               │
  │         GPIO 4 ├──────────┴─────────►│ DQ  (Pin 2)   │
  │            GND ├────────────────────►│ GND (Pin 1)   │
  └────────────────┘                     └───────────────┘
```

## ESP32 Sample Code (Core 3.x) — Reading Digital Temperature

```cpp
#include <OneWire.h>
#include <DallasTemperature.h>

const int oneWireBus = 4; // GPIO 4 on ESP32

OneWire oneWire(oneWireBus);
DallasTemperature sensors(&oneWire);

void setup() {
  Serial.begin(115200);
  sensors.begin();
  Serial.println("ESP32 DS18B20 1-Wire Sensor Initialized.");
}

void loop() {
  sensors.requestTemperatures(); 
  float tempC = sensors.getTempCByIndex(0);

  if (tempC == DEVICE_DISCONNECTED_C) {
    Serial.println("Error: DS18B20 sensor not detected!");
  } else {
    Serial.printf("Current Temperature: %.2f °C | %.2f °F\n", tempC, tempC * 9.0 / 5.0 + 32.0);
  }
  delay(1000);
}
```

## Source References
- Analog Devices / Maxim DS18B20 Programmable Resolution 1-Wire Digital Thermometer: https://www.analog.com/media/en/technical-documentation/data-sheets/DS18B20.pdf
- Miles Burton DallasTemperature Library: https://github.com/milesburton/Arduino-Temperature-Control-Library
