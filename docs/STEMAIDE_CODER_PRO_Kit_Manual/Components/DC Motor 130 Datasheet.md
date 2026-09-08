# DC Motor 130 — Technical Datasheet

## General Description

A standard 130-size permanent-magnet brush DC motor widely used in educational robotics, small vehicles, fans, and electromechanical demonstrators. The motor operates over a voltage range of 3.0V to 6.0V DC and draws between 150 mA (no-load) and 1.2 A (stall) [E1].

### Critical Drive & Power Mandate
> **GPIOs shall not directly drive motors, relay coils, high-current LEDs, or other loads requiring significant current. External transistor, MOSFET, or driver circuitry shall be used.** [E5]

Connecting a DC motor directly to an ESP32 GPIO will instantly destroy the microcontroller pin due to excessive current draw and inductive flyback voltage spikes ($V = -L \frac{di}{dt}$) reaching hundreds of volts [E1/E5]! 

The motor must be powered strictly from the **external 5V supply rail**, switched via an external **2N2222 NPN transistor (or TIP120 Darlington / L298N H-Bridge)**, and protected with a **1N4007 flyback diode** [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Motor Size** | Standard 130 Hobby Motor (20 × 15 × 25 mm) | [E1] |
| **Operating Voltage Range** | 3.0 V to 6.0 V DC (Nominal 5.0 V) | [E1] |
| **No-Load Speed** | 9,000 to 12,000 RPM (at 5.0 V) | [E1] |
| **No-Load Current** | 100 mA to 150 mA | [E1] |
| **Stall Current** | **800 mA to 1.2 A (at 5.0 V)** | [E1] |
| **Shaft Diameter / Length** | 2.0 mm diameter / 8.0 mm length | [E1] |
| **Speed Control Method** | Hardware PWM via LEDC peripheral | [E4] |

## Transistor Switching Schematic & Inductive Protection

```
               +5V External Power Rail
                   │
                   ├───[ 1N4007 Diode ]───┐  (Cathode to +5V; Flyback Clamp)
                   │      (Anode to Coll) │
                   ├───[ DC Motor 130 ]───┤
                   │                      │
                   │                      ▼ Collector
  ESP32 GPIO 25 ───┴───[ 1.0 kΩ ]───────►│  2N2222 NPN Transistor
                         (Base)          │ Emitter
                                         ├─── Common GND
```

- **Base Current Calculation**:
  $$I_B = \frac{V_{\text{GPIO}} - V_{BE}}{R_B} = \frac{3.3\text{ V} - 0.7\text{ V}}{1000\,\Omega} = 2.6\text{ mA} \quad [\text{E5}]$$
  With $h_{FE} \ge 100$, collector can switch up to 260 mA continuously (or use a TIP120 Darlington / MOSFET for full 1.2A stall headroom) [E5].

## ESP32 Sample Code (Core 3.x) — Motor Speed Control via LEDC

```cpp
const int motorPin = 25;     // Connects to 2N2222 base via 1 kΩ
const int pwmFreq = 5000;    // 5 kHz PWM frequency (inaudible humming)
const int pwmResolution = 8; // 8-bit resolution (0-255 duty cycle)

void setup() {
  Serial.begin(115200);
  // ESP32 Arduino Core 3.x LEDC API
  ledcAttach(motorPin, pwmFreq, pwmResolution);
  Serial.println("ESP32 DC Motor 130 Speed Controller Ready.");
}

void loop() {
  // Ramp motor speed up
  for (int duty = 0; duty <= 255; duty += 15) {
    ledcWrite(motorPin, duty);
    delay(100);
  }
  delay(1000);

  // Ramp motor speed down
  for (int duty = 255; duty >= 0; duty -= 15) {
    ledcWrite(motorPin, duty);
    delay(100);
  }
  delay(2000);
}
```

## Source References
- Mabuchi Motor 130 Series Technical Specifications: https://www.mabuchi-motor.com
- ON Semiconductor 2N2222 Datasheet: https://www.onsemi.com
