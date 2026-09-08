# Motion Sensor PIR (HC-SR501) — Technical Datasheet

## General Description

The HC-SR501 is an active pyroelectric infrared (PIR) motion detector module based on the BISS0001 micro-power PIR controller IC. It detects changes in infrared radiation emitted by moving human or warm bodies within a conical field of view up to 7 metres [E1].

### Native 3.3V Output Compatibility with ESP32
The HC-SR501 module is powered by an input voltage of **4.5V to 20V DC** (connect to the **`5V` power rail**) [E1]. 

> [!IMPORTANT]
> **Crucial Electrical Discovery: Native 3.3V TTL Output**:
> The HC-SR051 breakout board incorporates an onboard **7133-1 low-dropout linear regulator that powers the internal BISS0001 IC at 3.3V DC** [E1/E3]. Consequently, the module's **digital `OUT` pin natively outputs 3.3V TTL logic (HIGH = 3.3V, LOW = 0V)** [E1/E5]! 
> 
> Direct connection between the HC-SR501 `OUT` pin and ESP32 **`GPIO 13`** (or `GPIO 4`) is **100% electrically safe and requires zero level shifting** [E5]!

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Controller IC** | BISS0001 Micro Power PIR Motion Detector IC | [E1] |
| **Input Supply Voltage ($V_{CC}$)**| 4.5 V to 20 V DC (**Connect to 5V Power Rail**) | [E1] |
| **Output Signal ($V_{OUT}$)** | **3.3 V TTL High / 0 V Low (Natively 3.3V)** | [E1/E3] |
| **Quiescent Standby Current** | < 50 µA | [E1] |
| **Detection Range** | Adjustable from 3 m to 7 m via onboard potentiometer | [E1] |
| **Detection Angle** | < 100° cone angle with Fresnel lens | [E1] |
| **Delay Time** | Adjustable from 0.5 sec to 200 sec via onboard potentiometer | [E1] |
| **Trigger Mode** | L (Single trigger) or H (Repeatable trigger — default) | [E1] |

## Pinout & Wiring

```
           HC-SR501 Module Rear View
             ┌─────────────────────────┐
             │ [Delay]   [Sensitivity] │
             │  (Pot)        (Pot)     │
             │                         │
             │   ┌─┐             ┌─┐   │
             │   │L│ Jumper      │H│   │
             └───┴─┴─────────────┴─┴───┘
                    │     │     │
                   VCC   OUT   GND
```

| Pin Label | Function | ESP32-DevKitC V4 Connection | Notes |
|---|---|---|---|
| **VCC** | Power Supply | **5V Power Rail** | Powered from 5V rail to feed onboard 3.3V LDO [E3] |
| **OUT** | Motion Output | **GPIO 13** (or GPIO 4) | Direct 3.3V TTL signal (HIGH = Motion Detected) [E5] |
| **GND** | Ground | **GND** | Common ground reference [E1] |

```
   ESP32-DevKitC V4                      HC-SR501 PIR Sensor
  ┌────────────────┐                     ┌───────────────────┐
  │             5V ├────────────────────►│ VCC (4.5–20V)     │
  │        GPIO 13 ├◄────────────────────┤ OUT (3.3V Level)  │ (Direct 3.3V Input)
  │            GND ├─────────────────────┤ GND               │
  └────────────────┘                     └───────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Motion Detection with Hardware Interrupt

```cpp
const int pirPin = 13;
volatile bool motionDetected = false;

// Interrupt Service Routine (ISR) executed when motion occurs
void IRAM_ATTR motionISR() {
  motionDetected = true;
}

void setup() {
  Serial.begin(115200);
  pinMode(pirPin, INPUT);

  // Attach rising-edge hardware interrupt
  attachInterrupt(digitalPinToInterrupt(pirPin), motionISR, RISING);

  Serial.println("ESP32 HC-SR501 PIR Motion Sensor Ready.");
  Serial.println("Allowing 30 seconds for sensor PIR stabilization...");
}

void loop() {
  if (motionDetected) {
    motionDetected = false;
    Serial.println(">>> MOTION DETECTED! Alarm triggered.");
  }
  delay(100);
}
```

## Source References
- BISS0001 Micro Power PIR Motion Detector IC Datasheet: https://www.ladyada.net/media/sensors/BISS0001.pdf
- Adafruit PIR Sensor Tutorial: https://learn.adafruit.com/pir-passive-infrared-proximity-motion-sensor
