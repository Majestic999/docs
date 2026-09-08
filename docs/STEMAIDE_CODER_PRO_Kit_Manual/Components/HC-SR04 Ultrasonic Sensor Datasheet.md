# HC-SR04 Ultrasonic Sensor — Technical Datasheet

## General Description

The HC-SR04 is a non-contact ultrasonic distance measurement module operating at 40 kHz sonar frequency. It contains an ultrasonic transmitter, receiver, and control circuit. The module emits eight 40 kHz acoustic burst pulses upon receiving a 10 µs trigger pulse and outputs an active-high Echo pulse whose duration is proportional to target distance [E1].

### Mandatory Level Shifting on the ESP32 Platform
The HC-SR04 requires a **5.0V DC power supply** to generate sufficient acoustic transducer power [E1]. Consequently, its **Echo output pin produces a 5.0V TTL pulse** [E1/E3].

Connecting the 5.0V Echo pin directly to the ESP32 will permanently destroy the ESP32 GPIO input protection circuitry [E1]! A **mandatory passive resistor voltage divider (1.0 kΩ series / 2.0 kΩ shunt to GND)** must be installed to safely step the Echo pulse down to **3.33V** [E5].

The **Trigger pin** accepts standard TTL thresholds ($V_{IH} \ge 2.0\text{ V}$) and is driven directly by the ESP32's 3.3V CMOS output without level shifting [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Operating Voltage ($V_{CC}$)** | 5.0 V DC (Nominal; requires 5V supply rail) | [E1] |
| **Quiescent Current** | < 2 mA | [E1] |
| **Working Current** | 15 mA (active acoustic transmission) | [E1] |
| **Ultrasonic Frequency** | 40 kHz | [E1] |
| **Measurement Range** | 2 cm to 400 cm (0.02 m to 4.0 m) | [E1] |
| **Resolution / Accuracy** | 0.3 cm / ±3 mm | [E1] |
| **Measuring Angle** | 15° effective beam cone | [E1] |
| **Trigger Input Signal** | 10 µs TTL HIGH pulse (directly driven by 3.3V GPIO) | [E1/E5] |
| **Echo Output Signal** | 5.0 V TTL pulse width $\propto$ distance (**Requires Divider**) | [E1/E5] |

## Resistor Divider Calculation & Interfacing Schematic

$$V_{\text{Echo\_ESP32}} = 5.0\text{ V} \times \left( \frac{2000\,\Omega}{1000\,\Omega + 2000\,\Omega} \right) = 3.33\text{ V} \quad [\text{E5}]$$

```
   HC-SR04 Sensor                            ESP32-DevKitC V4
  ┌──────────────┐                          ┌────────────────┐
  │         VCC  ├──────────────────────────┤ 5V Power Rail  │
  │        Trig  ├──────────────────────────┤ GPIO 13        │ (Direct 3.3V drive)
  │        Echo  ├───[ 1.0 kΩ ]───┬─────────┤ GPIO 14        │ (Safe 3.33V pulse)
  │              │     (R1)       │         │                │
  │         GND  ├────────┐    [ 2.0 kΩ ]   │                │
  └──────────────┘        │       (R2)      │                │
                          │       │         │                │
                         GND ─────┴─────────┤ GND            │
```

## Distance Calculation Formula
Speed of sound in air at 20 °C is $\approx 343\text{ m/s} = 0.0343\text{ cm/µs}$ [E5]:

$$\text{Distance (cm)} = \frac{\text{Echo Pulse Width (µs)} \times 0.0343}{2} = \frac{\text{Echo Pulse Width (µs)}}{58.3} \quad [\text{E5}]$$

## ESP32 Sample Code (Core 3.x) — High-Precision Sonar Rangefinder

```cpp
const int trigPin = 13; // 3.3V Direct Output
const int echoPin = 14; // Input via 1k/2k Divider

void setup() {
  Serial.begin(115200);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  digitalWrite(trigPin, LOW);
  Serial.println("ESP32 HC-SR04 Rangefinder Initialized.");
}

void loop() {
  // Clear trigger
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  // Send 10 us HIGH pulse
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Read echo pulse duration (timeout after 30 ms = ~5 metres)
  long duration = pulseIn(echoPin, HIGH, 30000);

  if (duration == 0) {
    Serial.println("Out of range / No echo received.");
  } else {
    float distanceCm = duration / 58.3;
    float distanceInches = duration / 148.0;
    Serial.printf("Distance: %.1f cm (%.1f inches)\n", distanceCm, distanceInches);
  }

  delay(100); // Maintain > 60 ms cycle interval
}
```

## Source References
- ELECFreaks HC-SR04 Technical User Manual: https://www.elecfreaks.com
- SparkFun Ultrasonic Rangefinder Guide: https://learn.sparkfun.com
