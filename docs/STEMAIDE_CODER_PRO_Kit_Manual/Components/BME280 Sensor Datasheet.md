# BME280 Environmental Sensor (Temperature, Humidity, Pressure) — Technical Datasheet

## General Description

The BME280 is a combined digital environmental sensor developed by Bosch Sensortec that measures ambient temperature, relative humidity, and barometric pressure in a single compact package. It communicates with host microcontrollers via standard I²C or SPI interfaces [E1].

### Native Parity with the ESP32 Platform
The BME280 operates natively at **3.3V DC logic and supply voltage** ($V_{DD} = 1.71\text{V}–3.6\text{V}$) [E1]. On the legacy 5V Arduino UNO R3, connecting bare BME280 modules to 5V I²C lines risked silicon breakdown unless the module included an onboard regulator and level shifter. 

When interfaced with the **Espressif ESP32-DevKitC V4**, the BME280 connects directly to the ESP32's hardware I²C bus (**`GPIO 21 SDA`**, **`GPIO 22 SCL`**) and **`3V3` power rail**, providing 100% native voltage matching, higher bus speeds (up to 400 kHz Fast-Mode), and zero risk of overvoltage damage [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Sensor IC** | Bosch Sensortec BME280 | [E1] |
| **Operating Voltage ($V_{DD}$)** | 1.71 V to 3.6 V DC (**Connect to ESP32 3V3 Rail**) | [E1] |
| **Logic Level** | **3.3 V LVTTL** (Direct I²C / SPI bus connection) | [E1] |
| **Temperature Range / Accuracy** | −40 °C to +85 °C / ±0.5 °C (at 25 °C) | [E1] |
| **Humidity Range / Accuracy** | 0% to 100% RH / ±3% RH | [E1] |
| **Pressure Range / Accuracy** | 300 hPa to 1100 hPa (~9000m to −500m sea level) / ±1.0 hPa | [E1] |
| **Current Consumption** | 1.8 µA at 1 Hz (humidity/temp); 2.8 µA (pressure); 0.1 µA (sleep) | [E1] |
| **Default I²C Address** | `0x76` (SDO to GND) or `0x77` (SDO to VDD) | [E1] |
| **I²C Bus Frequency** | Standard-Mode (100 kHz) and Fast-Mode (400 kHz) | [E1/E4] |

## Pinout & Wiring

| Pin Label | Function | ESP32-DevKitC V4 Connection | Notes |
|---|---|---|---|
| **VCC / VIN** | Power Supply | **3V3 Rail** | 3.3V regulated power [E1] |
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **SCL / SCK** | I²C Clock | **GPIO 22** | Hardware I²C Clock (`Wire`) [E4] |
| **SDA / SDI** | I²C Data | **GPIO 21** | Hardware I²C Data (`Wire`) [E4] |
| **CSB / CS** | Chip Select | **3V3 Rail** (for I²C mode) | Pull HIGH for I²C; pull LOW for SPI [E1] |
| **SDO** | I²C Address Select | **GND** (`0x76`) or **3V3** (`0x77`)| Sets 7-bit slave address [E1] |

```
   ESP32-DevKitC V4                      BME280 Sensor Module
  ┌────────────────┐                     ┌────────────────────┐
  │            3V3 ├────────────────────►│ VCC (3.3V)         │
  │        GPIO 21 ├◄───────────────────►│ SDA (Hardware I2C) │
  │        GPIO 22 ├────────────────────►│ SCL (Hardware I2C) │
  │            GND ├─────────────────────┤ GND                │
  └────────────────┘                     └────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Weather Station Monitor

```cpp
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

#define SEALEVELPRESSURE_HPA (1013.25)

Adafruit_BME280 bme; // Uses default I2C Wire (SDA=21, SCL=22)

void setup() {
  Serial.begin(115200);
  Wire.begin(21, 22); // Explicitly bind ESP32 I2C pins

  // Try default address 0x76, then fallback to 0x77
  if (!bme.begin(0x76, &Wire)) {
    if (!bme.begin(0x77, &Wire)) {
      Serial.println("Could not find a valid BME280 sensor, check wiring!");
      while (1) delay(10);
    }
  }

  Serial.println("ESP32 BME280 Weather Station Initialized.");
}

void loop() {
  float temp = bme.readTemperature();
  float hum = bme.readHumidity();
  float press = bme.readPressure() / 100.0F; // Convert Pa to hPa
  float alt = bme.readAltitude(SEALEVELPRESSURE_HPA);

  Serial.printf("Temp: %.2f °C | Humidity: %.2f %% | Pressure: %.2f hPa | Altitude: %.1f m\n",
                temp, hum, press, alt);
  delay(2000);
}
```

## Source References
- Bosch Sensortec BME280 Datasheet: https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme280-ds002.pdf
- Adafruit BME280 Library: https://github.com/adafruit/Adafruit_BME280_Library
