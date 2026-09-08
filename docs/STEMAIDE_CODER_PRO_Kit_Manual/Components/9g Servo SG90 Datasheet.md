# 9g Micro Servo (SG90) — Technical Datasheet

## General Description

The SG90 is a lightweight, high-torque miniature analog servo motor capable of approximately 180° rotation. It contains an internal DC motor, reduction geartrain, feedback potentiometer, and proportional closed-loop control electronics [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, two strict engineering mandates apply:
1. **Power Isolation Mandate**: The servo motor draws up to **650 mA stall current**. It must **NEVER be powered from the ESP32 3V3 rail** or directly from an unbuffered GPIO pin! Power must be supplied from the external 5V power supply rail with common ground [E1/E5].
2. **Software Library Migration**: The legacy AVR `<Servo.h>` library relies on 8-bit Timer1 hardware interrupts that do not compile on ESP32. Software must use the **`<ESP32Servo.h>`** library, which leverages hardware LEDC PWM timers [E4].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Operating Voltage** | 4.8 V to 6.0 V DC (Nominal 5.0 V) | [E1] |
| **Operating Speed** | 0.12 sec / 60° (at 4.8 V) | [E1] |
| **Stall Torque** | 1.8 kg·cm (at 4.8 V) | [E1] |
| **Dead Band Width** | 7 µs (typical internal controller window) | [E1] |
| **Rotation Range** | ~180° (typically 0° to 180°) | [E1] |
| **PWM Control Frequency** | 50 Hz (20 ms period frame) | [E1] |
| **Pulse Width Range** | 500 µs (0°) to 2400 µs (180°); center at 1500 µs (90°) | [E1] |
| **Idle Current** | 10 mA to 15 mA | [E1] |
| **Stall Current** | 500 mA to 650 mA (at 5.0 V) | [E1] |
| **Signal Logic Level** | **3.3 V PWM Direct Drive Compatible** ($V_{IH} \approx 1.6\text{V}–2.0\text{V}$) | [E1/E3] |

## Wiring & Electrical Isolation

```
           +5V (External Power Rail)
               │
               ├─────────────────────────┐
               │                         │
      GND ─────┴──────────┐              │
                          │              │
   ESP32-DevKitC V4       │              │      SG90 Servo
  ┌────────────────┐      │              │     ┌───────────┐
  │        GPIO 27 ├──────┼──────────────┼────►│ Signal    │ (Orange / Yellow)
  │                │      │              └────►│ VCC (5V)  │ (Red)
  │            GND ├──────┴───────────────────►│ GND       │ (Brown)
  └────────────────┘                           └───────────┘
```

> [!WARNING]
> **Power Rail Protection**:
> Connecting the red servo wire to the ESP32 3V3 pin will cause immediate microcontroller brownout resets during servo motion and risks destroying the onboard 3.3V linear regulator [E5]!

## ESP32 Sample Code (Core 3.x) — Sweep Using `ESP32Servo`

```cpp
#include <ESP32Servo.h>

Servo myServo;
const int servoPin = 27; // Safe general-purpose GPIO

void setup() {
  Serial.begin(115200);

  // Allocate all available ESP32 hardware PWM timers
  ESP32PWM::allocateTimer(0);
  ESP32PWM::allocateTimer(1);
  ESP32PWM::allocateTimer(2);
  ESP32PWM::allocateTimer(3);

  myServo.setPeriodHertz(50);          // Standard 50 Hz servo PWM
  myServo.attach(servoPin, 500, 2400); // Min pulse 500 us, max pulse 2400 us
  Serial.println("ESP32 SG90 Servo Sweep Initialized.");
}

void loop() {
  // Sweep from 0 to 180 degrees
  for (int pos = 0; pos <= 180; pos += 5) {
    myServo.write(pos);
    delay(20);
  }
  // Sweep back from 180 to 0 degrees
  for (int pos = 180; pos >= 0; pos -= 5) {
    myServo.write(pos);
    delay(20);
  }
}
```

## Source References
- TowerPro SG90 Official Datasheet: http://www.towerpro.com.tw/product/sg90-7/
- ESP32Servo Library Repository: https://github.com/madhephaestus/ESP32Servo
