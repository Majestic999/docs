# Relay Module 1-Channel — Technical Datasheet

## General Description

A 1-channel electromechanical relay breakout board designed for switching high-voltage or high-current AC/DC loads (up to 250VAC @ 10A or 30VDC @ 10A) under microcontroller control. The module includes an SPDT relay, an optocoupler (EL817 / PC817) for optical galvanic isolation, a freewheeling flyback diode, a switching transistor, and indicator LEDs [E1].

### Critical Electrical Hazard: Active-LOW Optocoupler Latch-On
Most standard 1-channel relay modules use an active-LOW optocoupler input where the optocoupler's internal infrared LED is tied to the board's `VCC` pin (5.0V). When an ESP32 outputs a 3.3V HIGH to turn the relay OFF:

$$\Delta V = 5.0\text{ V} - 3.3\text{ V} = 1.7\text{ V} \quad [\text{E5}]$$

Because an infrared optocoupler LED conducts at $1.2\text{V}–1.4\text{V}$, the 1.7V drop is sufficient to keep the optocoupler permanently turned ON, meaning the relay **never turns off** [E3/E5]!

### Mandatory Mitigation: JD-VCC Isolation
1. **Remove the yellow shorting jumper** bridging `VCC` and `JD-VCC` on the relay board [E3].
2. Connect `JD-VCC` directly to the **5V power supply rail** (powers the relay coil) [E5].
3. Connect `VCC` to the **ESP32 3V3 rail** (powers the optocoupler anode). When GPIO outputs 3.3V HIGH, $\Delta V = 3.3\text{V} - 3.3\text{V} = 0\text{V}$, turning the relay completely OFF [E5]!
4. Connect `IN` to **`GPIO 25`**. Connect `GND` to common system ground [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Relay Model** | Songle SRD-05VDC-SL-C (or compatible SPDT relay) | [E1] |
| **Coil Nominal Voltage** | 5.0 V DC | [E1] |
| **Coil Operating Current** | ~70 mA to 80 mA (when energized; powered from 5V rail) | [E1] |
| **Contact Ratings** | 10A 250VAC / 10A 125VAC / 10A 30VDC / 10A 28VDC | [E1] |
| **Contact Configuration** | SPDT: Common (COM), Normally Open (NO), Normally Closed (NC)| [E1] |
| **Optocoupler Isolation** | PC817 / EL817 Optical Isolator (5000 V RMS isolation) | [E1] |
| **Trigger Logic** | Active-LOW (or Active-HIGH depending on module revision) | [E1] |

## Interfacing Schematic (JD-VCC Jumper Removed)

```
   ESP32-DevKitC V4                      1-Channel Relay Breakout
  ┌────────────────┐                     ┌────────────────────────┐
  │             5V ├────────────────────►│ JD-VCC (Jumper REMOVED)│ (Powers 5V coil)
  │            3V3 ├────────────────────►│ VCC                    │ (Powers 3.3V opto)
  │        GPIO 25 ├────────────────────►│ IN                     │ (Active-LOW trigger)
  │            GND ├─────────────────────┤ GND                    │
  └────────────────┘                     └────────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Safe Relay Switching

```cpp
const int relayPin = 25; // Safe general-purpose GPIO

void setup() {
  Serial.begin(115200);
  pinMode(relayPin, OUTPUT);

  // For active-LOW relay with JD-VCC isolation, HIGH = DE-ENERGIZED (OFF)
  digitalWrite(relayPin, HIGH); 
  Serial.println("ESP32 Relay Controller Initialized (Relay is OFF).");
}

void loop() {
  Serial.println(">>> Energizing Relay (ON)...");
  digitalWrite(relayPin, LOW); // Active-LOW: Turn relay ON
  delay(3000);

  Serial.println("--- De-energizing Relay (OFF)...");
  digitalWrite(relayPin, HIGH); // Turn relay OFF
  delay(3000);
}
```

## Source References
- Songle SRD Relay Datasheet: https://www.songle.com
- Sharp / Everlight PC817 Optocoupler Datasheet: https://www.everlight.com
