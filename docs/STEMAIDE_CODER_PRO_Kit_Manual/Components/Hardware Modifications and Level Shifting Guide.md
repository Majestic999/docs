# Hardware Modifications and Level Shifting Guide

## 1. Executive Summary & Electrical Safety Policy

The STEMAIDE Coder Pro Kit contains components originally designed for 5.0V microcontroller environments (Arduino UNO R3). Because the **Espressif ESP32-DevKitC V4** operates strictly at **3.3V LVTTL logic** and is **NOT 5V tolerant**, connecting 5V digital signals or high-voltage pull-ups directly to ESP32 GPIOs will permanently damage the silicon gate oxide and ESD protection diodes [E1].

This guide provides fully verified, step-by-step schematics, resistor calculations, and wiring instructions for every component requiring level shifting, voltage division, or external power isolation [E5].

---

## 2. Modification 1: HC-SR04 Ultrasonic Sensor Echo Voltage Divider

### The Electrical Hazard
The HC-SR04 ultrasonic sensor operates from 5V VCC and outputs a 5V TTL pulse on its Echo pin proportional to target distance. Connecting Echo directly to an ESP32 GPIO exposes the pin to 5.0V, violating the 3.6V absolute maximum rating [E1/E3].

### Engineering Calculation & Schematic
A passive resistor voltage divider is implemented between the Echo pin and ESP32 GPIO 14 using standard kit resistors ($R_1 = 1.0\text{ k}\Omega$, $R_2 = 2.0\text{ k}\Omega$ or two $1.0\text{ k}\Omega$ in series) [E5]:

$$V_{\text{out}} = V_{\text{in}} \times \left( \frac{R_2}{R_1 + R_2} \right) = 5.0\text{ V} \times \left( \frac{2000}{1000 + 2000} \right) = 3.33\text{ V}$$

```
   HC-SR04 Sensor                            ESP32-DevKitC V4
  ┌──────────────┐                          ┌────────────────┐
  │         VCC  ├──────────────────────────┤ 5V (or Ext 5V) │
  │              │                          │                │
  │        Trig  ├──────────────────────────┤ GPIO 13        │ (Direct 3.3V drive)
  │              │                          │                │
  │        Echo  ├───[ 1.0 kΩ ]───┬─────────┤ GPIO 14 (Echo) │ (Safe 3.33V input)
  │              │     (R1)       │         │                │
  │         GND  ├────────┐    [ 2.0 kΩ ]   │                │
  └──────────────┘        │       (R2)      │                │
                          │       │         │                │
                         GND ─────┴─────────┤ GND            │
```

- **Trigger Pin Drive**: The HC-SR04 input comparator has a TTL high threshold $V_{IH} \ge 2.0\text{ V}$ [E3]. The ESP32 delivers $3.3\text{ V}$ ($> 2.4\text{ V}$ standard TTL high), driving the Trig pin reliably without level shifting [E1/E5].

---

## 3. Modification 2: IIC 1602 LCD Backpack Logic Level Translation

### The Electrical Hazard
The standard PCF8574 I²C backpack requires 5.0V VCC to drive the HD44780 LCD character contrast (at 3.3V, the display is completely blank or faint) [E3]. However, the backpack includes two $4.7\text{ k}\Omega$ pull-up resistors tied internally to its 5V rail. If connected directly to ESP32 I²C pins (`GPIO 21` and `GPIO 22`), current flows into the ESP32's ESD clamping diodes, causing bus corruption or silicon degradation [E1/E5].

### Prescribed Interfacing Options
- **Method A (Recommended): Bidirectional MOSFET Level Shifter (BSS138 / PCA9306)**
  Use an external 4-channel bidirectional level shifter module:
  - Low-Voltage Side (LV): Connect LV to ESP32 `3V3`, GND to `GND`. Connect LV1 to `GPIO 21 (SDA)`, LV2 to `GPIO 22 (SCL)`.
  - High-Voltage Side (HV): Connect HV to `5V`, GND to `GND`. Connect HV1 to LCD `SDA`, HV2 to LCD `SCL`.
- **Method B (Hardware Modification): Remove 5V Pull-up Resistors on Backpack**
  Using a fine soldering tip, de-solder or cut the two SMD pull-up resistors ($4.7\text{ k}\Omega$, marked `472`) located next to the SDA/SCL pins on the backpack. Then rely on ESP32 internal pull-ups or connect external $4.7\text{ k}\Omega$ resistors to the 3.3V rail [E3/E5].

```
   ESP32-DevKitC              Bidirectional Level Shifter             IIC 1602 LCD Backpack
  ┌─────────────┐             ┌─────────────────────────┐             ┌─────────────────────┐
  │         3V3 ├─────────────┤ LV                   HV ├─────────────┤ VCC (5V)            │
  │     GPIO 21 ├─────────────┤ LV1 (SDA)     HV1 (SDA) ├─────────────┤ SDA                 │
  │     GPIO 22 ├─────────────┤ LV2 (SCL)     HV2 (SCL) ├─────────────┤ SCL                 │
  │         GND ├─────────────┤ GND                 GND ├─────────────┤ GND                 │
  └─────────────┘             └─────────────────────────┘             └─────────────────────┘
```

---

## 4. Modification 3: 1-Channel Relay Module Active-LOW Isolation

### The Electrical Hazard
Most 1-channel relay breakout boards utilize an active-LOW optocoupler input. The optocoupler anode and its series status LED are tied to the board's `VCC` pin (5.0V). When an ESP32 outputs a digital HIGH (3.3V) to turn the relay OFF:

$$\Delta V = V_{\text{CC}} - V_{\text{GPIO}} = 5.0\text{ V} - 3.3\text{ V} = 1.7\text{ V}$$

Because an infrared optocoupler LED has a forward drop of only $1.2\text{V}–1.4\text{V}$, the $1.7\text{V}$ difference is sufficient to keep the optocoupler conducting, causing the relay to **remain permanently stuck ON** [E3/E5]!

### Prescribed Engineering Solutions
- **Method 1: JD-VCC Jumper Isolation (For 3-pin + 3-pin headers)**
  1. Remove the yellow shorting jumper bridging `VCC` and `JD-VCC`.
  2. Connect `JD-VCC` directly to the **5V power supply rail** (powers the electromagnetic relay coil).
  3. Connect `VCC` directly to the **ESP32 3V3 rail** (powers the optocoupler LED). Now, when GPIO outputs 3.3V HIGH, $\Delta V = 3.3\text{V} - 3.3\text{V} = 0\text{V}$, turning the relay completely OFF [E3/E5]!
  4. Connect `IN` to `GPIO 25`. Connect `GND` to common system ground.
- **Method 2: NPN Transistor Buffer (For modules without JD-VCC jumper)**
  Drive the `IN` pin using a 2N2222 or 2N3904 NPN transistor:
  - Base: Connect to `GPIO 25` through a $1.0\text{ k}\Omega$ resistor.
  - Collector: Connect to relay `IN` pin.
  - Emitter: Connect to GND.
  - Logic: Inverted in software (setting GPIO HIGH turns transistor ON, pulling `IN` to GND and energizing relay) [E5].

---

## 5. Modification 4: MAX7219 8x8 Dot Matrix Logic Buffering

### The Electrical Hazard
According to the official Maxim MAX7219 datasheet, the minimum high-level input voltage is:

$$V_{IH\min} = 3.5\text{ V} \quad (\text{at } V_{\text{CC}} = 5.0\text{ V}) \quad [\text{E1}]$$

The ESP32 outputs $3.3\text{V}$, which is below the guaranteed $3.5\text{V}$ threshold. While some clone ICs happen to trigger at $2.4\text{V}$, authentic chips or setups with longer jumper wires suffer from random pixel corruption or missing frames [E1/E6].

### Prescribed Interfacing: 74HCT125 / 74AHCT125 Non-Inverting Buffer
Use an intermediate 74HCT-family logic buffer powered at 5V. The "T" family (TTL compatible) has an input threshold of $V_{IH\min} = 2.0\text{ V}$ and outputs a full 5V CMOS signal [E1]:
- Connect 74HCT125 VCC to 5V, GND to common ground.
- Tie all Output Enable pins (OE1–OE3) to GND (permanently enabled).
- Input 1A (`GPIO 23 MOSI`) $\to$ Output 1Y (`MAX7219 DIN`)
- Input 2A (`GPIO 18 SCK`) $\to$ Output 2Y (`MAX7219 CLK`)
- Input 3A (`GPIO 27 CS`) $\to$ Output 3Y (`MAX7219 CS`)

> [!CAUTION]
> **Prohibited Practice: Series Diode Hack**
> Inserting a silicon diode in series with the MAX7219 5V supply rail to artificially lower VCC is an uncalibrated hack that degrades LED display brightness, causes thermal drift, and is strictly prohibited in STEMAIDE production guides [E5].

---

## 6. Modification 5: Motor & High-Current Actuator Power Rail Isolation

### The Universal Drive Rule
> **GPIOs shall not directly drive motors, relay coils, high-current LEDs, or other loads requiring significant current. External transistor, MOSFET, or driver circuitry shall be used.** [E5]

### Actuator Interfacing Specifications

| Actuator | Stall / Peak Current | Logic Drive Voltage | Power Supply Rail | Interfacing Circuit |
|---|---|---|---|---|
| **SG90 Micro Servo** | 650 mA stall [E3] | 3.3V PWM (50 Hz) | External 5V Regulated | PWM directly to `GPIO 27`; Power from 5V rail; Common GND [E1/E3] |
| **DC Motor 130** | 1.2 A stall [E3] | 3.3V PWM (LEDC) | External 5V / Battery | 2N2222 / TIP120 transistor or L298N; 1N4007 flyback diode [E5] |
| **28BYJ-48 Stepper** | 240 mA peak [E3] | 3.3V Digital (IN1–4) | External 5V Regulated | ULN2003 Darlington array; Base directly driven by 3.3V GPIOs [E1/E5] |
| **Active Buzzer** | 30 mA continuous [E3]| 3.3V Digital | External 5V or 3.3V | 2N2222 NPN switch with 1 kΩ base resistor [E5] |

### DC Motor 130 Transistor Schematic

```
               +5V (External Supply Rail)
                   │
                   ├───[ 1N4007 Diode ]───┐  (Cathode to +5V)
                   │      (Flyback)       │
                   ├───[ DC Motor 130 ]───┤
                   │                      │
                   │                      ▼ Collector
  ESP32 GPIO 25 ───┴───[ 1.0 kΩ ]───────►│  2N2222 NPN Transistor
                         (Base)           │ Emitter
                                          ├─── Common GND
```
