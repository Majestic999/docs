# RGB 3-Colour LED Module (KY-016) — Technical Datasheet

## General Description

A full-colour light-emitting diode breakout board containing three independent red, green, and blue LED dies integrated into a single 5mm diffused package with a common cathode terminal. By driving the R, G, and B pins with pulse-width modulated (PWM) signals, over 16 million distinct colours can be mixed [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, the three color channels connect directly to safe general-purpose GPIOs (**`GPIO 25 Red`**, **`GPIO 26 Green`**, **`GPIO 27 Blue`**), modulated using the ESP32's hardware **LEDC peripheral** with clean Core 3.x APIs (`ledcAttach` / `ledcWrite`) [E1/E4].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Module Type** | KY-016 5mm Diffused RGB LED Module | [E1] |
| **Configuration** | Common Cathode (Cathode tied to GND) | [E1] |
| **Red Forward Voltage ($V_F$)** | 1.8 V to 2.0 V ($I_F = 20\text{ mA}$) | [E1] |
| **Green Forward Voltage ($V_F$)**| 2.8 V to 3.2 V ($I_F = 20\text{ mA}$) | [E1] |
| **Blue Forward Voltage ($V_F$)** | 3.0 V to 3.2 V ($I_F = 20\text{ mA}$) | [E1] |
| **Series Resistors on Board** | Integrated 150 Ω / 220 Ω resistors (or external resistors required)| [E1/E5] |
| **Drive Current at 3.3V** | Red: ~6.4 mA; Green: ~2.5 mA; Blue: ~2.0 mA | [E5] |
| **PWM Architecture** | 3 independent hardware channels via ESP32 LEDC | [E4] |

## Pinout & Wiring

```
         KY-016 RGB Module Pinout
              ┌───┬───┬───┬───┐
              │ − │ R │ G │ B │
              └───┴───┴───┴───┘
                │   │   │   │
               GND  Red Grn Blu
```

| Pin Label | Function | ESP32 GPIO Connection | Description |
|---|---|---|---|
| **− (GND)** | Common Cathode | **GND** | Connects to common ground [E1] |
| **R** | Red Anode | **GPIO 25** | LEDC PWM Channel (150–220 Ω resistor) [E5] |
| **G** | Green Anode | **GPIO 26** | LEDC PWM Channel (100–150 Ω resistor) [E5] |
| **B** | Blue Anode | **GPIO 27** | LEDC PWM Channel (100–150 Ω resistor) [E5] |

## ESP32 Sample Code (Core 3.x) — True 24-Bit Colour Cycling

```cpp
const int redPin = 25;
const int greenPin = 26;
const int bluePin = 27;

const int pwmFreq = 5000;    // 5 kHz PWM frequency
const int pwmResolution = 8; // 8-bit resolution (0-255 per channel)

void setColor(byte r, byte g, byte b) {
  ledcWrite(redPin, r);
  ledcWrite(greenPin, g);
  ledcWrite(bluePin, b);
}

void setup() {
  Serial.begin(115200);

  // ESP32 Arduino Core 3.x unified LEDC API
  ledcAttach(redPin, pwmFreq, pwmResolution);
  ledcAttach(greenPin, pwmFreq, pwmResolution);
  ledcAttach(bluePin, pwmFreq, pwmResolution);

  Serial.println("ESP32 RGB LED Controller Ready.");
}

void loop() {
  // Red
  setColor(255, 0, 0);
  delay(1000);
  // Green
  setColor(0, 255, 0);
  delay(1000);
  // Blue
  setColor(0, 0, 255);
  delay(1000);
  // Yellow (Red + Green)
  setColor(255, 255, 0);
  delay(1000);
  // Cyan (Green + Blue)
  setColor(0, 255, 255);
  delay(1000);
  // Magenta (Red + Blue)
  setColor(255, 0, 255);
  delay(1000);
  // White (Red + Green + Blue)
  setColor(255, 255, 255);
  delay(1000);
}
```

## Source References
- Everlight Optoelectronics 5mm Full-Color RGB LED Specifications: https://www.everlight.com
