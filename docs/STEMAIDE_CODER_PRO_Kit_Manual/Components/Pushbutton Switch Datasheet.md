# Pushbutton Switch (Tactile) — Technical Datasheet

## General Description

A 4-pin momentary tactile pushbutton switch. Pressing the button bridges the internal gold/silver-plated dome contact to close the circuit; releasing the button opens the circuit via internal spring return [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, the switch connects directly between a bidirectional GPIO (e.g. **`GPIO 13`**) and **`GND`**, utilizing the ESP32's internal programmable pull-up resistor (**`pinMode(13, INPUT_PULLUP)`**) without requiring external pull-up resistors [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Switch Type** | SPST-NO (Single Pole Single Throw, Normally Open) | [E1] |
| **Dimensions** | 6.0 × 6.0 × 5.0 mm tactile switch | [E1] |
| **Contact Rating** | 12 V DC @ 50 mA | [E1] |
| **Contact Resistance** | $\le 100\text{ m}\Omega$ | [E1] |
| **Bounce Duration** | $\le 5\text{ ms}$ (software debounce recommended: 20 ms) | [E1/E4] |
| **Operating Force** | 160 ± 50 gf | [E1] |
| **Operating Life** | $\ge 100,000$ actuations | [E1] |
| **Logic Configuration** | **Active LOW** (using ESP32 internal `INPUT_PULLUP`) | [E5] |

## Internal Pin Connections & Wiring

```
         Tactile Switch Internal Connections
             Pin 1 ─────── Pin 2   (Internally tied)
               │             │
              [ ] Actuator  [ ]
               │             │
             Pin 3 ─────── Pin 4   (Internally tied)

   ESP32-DevKitC V4                    Tactile Pushbutton
  ┌────────────────┐                   ┌───────────────────┐
  │        GPIO 13 ├───────────────────┤ Pin 1 (or 2)      │
  │ (INPUT_PULLUP) │                   │                   │
  │            GND ├───────────────────┤ Pin 3 (or 4)      │
  └────────────────┘                   └───────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Pushbutton Toggle with Debouncing

```cpp
const int buttonPin = 13; // Bidirectional GPIO with pull-up

int buttonState = HIGH;
int lastButtonState = HIGH;
unsigned long lastDebounceTime = 0;
const unsigned long debounceDelay = 30; // 30 ms debounce window
bool systemActive = false;

void setup() {
  Serial.begin(115200);
  pinMode(buttonPin, INPUT_PULLUP);
  Serial.println("ESP32 Tactile Pushbutton Initialized.");
}

void loop() {
  int reading = digitalRead(buttonPin);

  if (reading != lastButtonState) {
    lastDebounceTime = millis();
  }

  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (reading != buttonState) {
      buttonState = reading;
      if (buttonState == LOW) { // Button pressed
        systemActive = !systemActive;
        Serial.printf("Button Pressed -> System State: %s\n", systemActive ? "ACTIVE" : "STANDBY");
      }
    }
  }

  lastButtonState = reading;
}
```

## Source References
- Omron B3F Tactile Switch Datasheet: https://components.omron.com
