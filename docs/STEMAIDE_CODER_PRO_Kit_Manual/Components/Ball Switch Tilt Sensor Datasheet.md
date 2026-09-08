# Ball Switch Tilt Sensor — Technical Datasheet

## General Description

A mechanical tilt sensor containing a pair of conductive rolling metal balls inside a sealed cylindrical housing with two electrical contacts. When oriented upright or tilted beyond ~15°, the metal balls bridge the internal gold-plated contacts, closing the circuit. When tilted in the opposite direction or inverted, gravity moves the balls away, opening the circuit [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, the sensor connects directly between a bidirectional GPIO (e.g. `GPIO 13`) and `GND`, utilizing the ESP32's internal programmable pull-up resistor (`INPUT_PULLUP`). Because mechanical rolling balls suffer from contact bounce, software debouncing or hardware filtering is required [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Sensor Model** | SW-200D / SW-520D (typical rolling ball tilt switch) | [E1] |
| **Max Operating Voltage** | 12 V DC | [E1] |
| **Max Current** | 20 mA | [E1] |
| **Contact Resistance** | < 10 Ω (closed state) | [E1] |
| **Open Resistance** | > 10 MΩ (open state) | [E1] |
| **Operating Tilt Angle** | ~15° from horizontal | [E1] |
| **Mechanical Life** | $\ge 100,000$ operations | [E1] |
| **Logic Level** | **3.3 V LVTTL** (via internal `INPUT_PULLUP`) | [E5] |

## Wiring Diagram

```
   ESP32-DevKitC V4                    Tilt Sensor (SW-520D)
  ┌────────────────┐                   ┌─────────────────────┐
  │        GPIO 13 ├───────────────────┤ Terminal 1          │
  │ (INPUT_PULLUP) │                   │                     │
  │            GND ├───────────────────┤ Terminal 2          │
  └────────────────┘                   └─────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Tilt Detection with Software Debounce

```cpp
const int tiltPin = 13; // Uses internal pull-up

int lastTiltState = HIGH;
unsigned long lastDebounceTime = 0;
const unsigned long debounceDelay = 50; // 50 ms debounce window

void setup() {
  Serial.begin(115200);
  pinMode(tiltPin, INPUT_PULLUP);
  Serial.println("ESP32 Ball Tilt Sensor Initialized.");
}

void loop() {
  int reading = digitalRead(tiltPin);

  if (reading != lastTiltState) {
    lastDebounceTime = millis();
  }

  if ((millis() - lastDebounceTime) > debounceDelay) {
    static int currentTiltState = HIGH;
    if (reading != currentTiltState) {
      currentTiltState = reading;
      if (currentTiltState == LOW) {
        Serial.println(">>> SENSOR TILTED (Contact Closed)");
      } else {
        Serial.println("--- Sensor Upright (Contact Open)");
      }
    }
  }

  lastTiltState = reading;
}
```

## Source References
- Comus International Tilt Switch Technical Guide: https://www.comus-intl.com
