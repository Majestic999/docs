# IIC 1602 LCD — Technical Datasheet

## General Description

A 16-character by 2-row alphanumeric liquid crystal display (LCD) based on the HD44780 controller, integrated with a PCF8574 8-bit I/O expander backpack. The backpack converts 16 parallel display control lines into a standard 2-wire I²C serial interface (SDA, SCL), drastically reducing microcontroller pin usage [E1].

### Mandatory Level Shifting on the ESP32 Platform
The HD44780 liquid crystal fluid requires **5.0V VCC** to establish proper display contrast (at 3.3V, characters appear completely invisible or washed out) [E1/E3]. However, the PCF8574 backpack includes two onboard **$4.7\text{ k}\Omega$ pull-up resistors tied directly to 5V VCC** [E3]. 

Connecting the backpack directly to an ESP32 injects 5V current into the ESP32's 3.3V I²C pins (`GPIO 21` and `GPIO 22`), violating the 3.6V maximum rating [E1/E5]! A **bidirectional MOSFET logic level shifter (BSS138)** must be installed between the ESP32 and the LCD backpack [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Display Capacity** | 16 characters × 2 rows (5×8 pixel matrix font) | [E1] |
| **Controller IC** | HD44780 (or compatible LCD driver) | [E1] |
| **I²C Backpack IC** | NXP PCF8574 / PCF8574A I/O Expander | [E1] |
| **Operating Voltage ($V_{CC}$)** | 5.0 V DC (Required for display contrast & backlight) | [E1/E3] |
| **Backlight Current** | ~40 mA to 50 mA (powered from 5V rail) | [E1] |
| **Logic Supply Current** | ~2 mA | [E1] |
| **Default I²C Address** | `0x27` (PCF8574T) or `0x3F` (PCF8574AT) | [E1] |
| **I²C Address Jumpers** | A0, A1, A2 solder pads on backpack (configurable 0x20–0x27) | [E1] |
| **I²C Logic Level** | **5.0 V (Requires Bidirectional Level Shifter)** | [E5] |

## Interfacing Architecture via Bidirectional Level Shifter

```
   ESP32-DevKitC V4             Bidirectional Level Shifter             IIC 1602 LCD Backpack
  ┌────────────────┐            ┌─────────────────────────┐             ┌─────────────────────┐
  │            3V3 ├────────────┤ LV                   HV ├─────────────┤ VCC (5V Ext Rail)   │
  │        GPIO 21 ├────────────┤ LV1 (SDA)     HV1 (SDA) ├─────────────┤ SDA                 │
  │        GPIO 22 ├────────────┤ LV2 (SCL)     HV2 (SCL) ├─────────────┤ SCL                 │
  │            GND ├────────────┤ GND                 GND ├─────────────┤ GND                 │
  └────────────────┘            └─────────────────────────┘             └─────────────────────┘
```

## I²C Address Selection (PCF8574)

| A2 Pad | A1 Pad | A0 Pad | PCF8574 Address | PCF8574A Address |
|---|---|---|---|---|
| Open | Open | Open | **0x27 (Default)** | **0x3F (Default)** |
| Open | Open | Short | 0x26 | 0x3E |
| Short | Short | Short | 0x20 | 0x38 |

## ESP32 Sample Code (Core 3.x) — LCD Counter & Status Display

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// Set the LCD I2C address (typically 0x27 or 0x3F)
LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  Serial.begin(115200);
  Wire.begin(21, 22); // Bind ESP32 Hardware I2C (SDA=21, SCL=22)

  lcd.init();
  lcd.backlight();

  lcd.setCursor(0, 0);
  lcd.print("STEMAIDE ESP32");
  lcd.setCursor(0, 1);
  lcd.print("Coder Pro Ready!");
  Serial.println("ESP32 IIC 1602 LCD Initialized.");
}

void loop() {
  static unsigned long lastTick = 0;
  static int seconds = 0;

  if (millis() - lastTick >= 1000) {
    lastTick = millis();
    seconds++;

    lcd.setCursor(0, 1);
    lcd.print("Uptime: ");
    lcd.print(seconds);
    lcd.print("s   ");
  }
}
```

## Source References
- NXP PCF8574 Remote 8-Bit I/O Expander for I2C-Bus: https://www.nxp.com
- LiquidCrystal_I2C Library by Frank de Brabander: https://github.com/johnrickman/LiquidCrystal_I2C
