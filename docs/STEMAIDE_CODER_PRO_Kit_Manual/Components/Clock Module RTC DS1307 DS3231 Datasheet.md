# Real-Time Clock Module (DS1307 / DS3231) — Technical Datasheet

## General Description

A battery-backed Real-Time Clock (RTC) module providing continuous, accurate timekeeping (seconds, minutes, hours, day, date, month, year with leap-year compensation up to 2100) even when the host microcontroller is powered off [E1]. The kit supports either the standard DS1307 or the precision temperature-compensated DS3231 [E1].

### Architectural Compatibility with ESP32
- **DS3231 Module (Preferred)**: Operates natively from $2.3\text{V to }5.5\text{V DC}$. Connects directly to the **ESP32 3V3 rail** and hardware I²C pins (**`GPIO 21 SDA`**, **`GPIO 22 SCL`**) without level shifting [E1/E5].
- **DS1307 Module**: Operates strictly at $5.0\text{V DC}$ ($V_{CC\min} = 4.5\text{V}$). Its onboard I²C pull-up resistors tie SDA and SCL to 5V. When using the DS1307, a **bidirectional MOSFET logic level shifter (BSS138)** is mandatory to protect the ESP32's 3.3V I²C pins [E1/E5].
- **ESP32 Internal RTC**: The ESP32 also features an internal software RTC capable of synchronizing automatically to global atomic time via Wi-Fi Network Time Protocol (SNTP) [E1/E4].

## Specifications Comparison

| Parameter | Precision DS3231 Module | Standard DS1307 Module | Evidence Tier |
|---|---|---|---|
| **Operating Voltage ($V_{CC}$)** | **2.3 V to 5.5 V DC (Use 3.3V)** | **4.5 V to 5.5 V DC (Requires 5V)** | [E1] |
| **Logic Compatibility with ESP32**| **Direct 3.3V I²C Compatible** | **Requires I²C Level Shifter** | [E5] |
| **Oscillator Type** | Integrated Temperature-Compensated (TCXO)| External 32.768 kHz tuning-fork crystal | [E1] |
| **Timekeeping Accuracy** | ±2 ppm (~1 minute drift per year) | ±20 ppm (~1–2 minutes drift per month)| [E1] |
| **Backup Battery** | CR2032 3V Lithium coin cell | CR2032 or LIR2032 rechargeable cell | [E1] |
| **I²C Bus Address** | `0x68` (Fixed) | `0x68` (Fixed) | [E1] |
| **Integrated EEPROM** | 32 KB AT24C32 on board (I²C address `0x57`)| 32 KB AT24C32 on board | [E1] |

## Wiring — DS3231 (Direct 3.3V Connection)

```
   ESP32-DevKitC V4                      DS3231 RTC Module
  ┌────────────────┐                     ┌─────────────────┐
  │            3V3 ├────────────────────►│ VCC (3.3V)      │
  │        GPIO 21 ├◄───────────────────►│ SDA             │
  │        GPIO 22 ├────────────────────►│ SCL             │
  │            GND ├─────────────────────┤ GND             │
  └────────────────┘                     └─────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Reading Date & Time

```cpp
#include <Wire.h>
#include "RTClib.h"

RTC_DS3231 rtc;

void setup() {
  Serial.begin(115200);
  Wire.begin(21, 22); // Bind ESP32 Hardware I2C

  if (!rtc.begin(&Wire)) {
    Serial.println("Couldn't find RTC module!");
    while (1) delay(10);
  }

  if (rtc.lostPower()) {
    Serial.println("RTC lost power, setting time to compile time...");
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));
  }

  Serial.println("ESP32 RTC DS3231 Initialized.");
}

void loop() {
  DateTime now = rtc.now();

  Serial.printf("%04d-%02d-%02d %02d:%02d:%02d | Temp: %.2f °C\n",
                now.year(), now.month(), now.day(),
                now.hour(), now.minute(), now.second(),
                rtc.getTemperature());

  delay(1000);
}
```

## Source References
- Analog Devices / Maxim DS3231 Datasheet: https://www.analog.com/media/en/technical-documentation/data-sheets/DS3231.pdf
- Adafruit RTClib Library: https://github.com/adafruit/RTClib
