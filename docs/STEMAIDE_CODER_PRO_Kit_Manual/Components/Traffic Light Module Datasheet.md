# Traffic Light LED Module — Technical Datasheet

## General Description

An educational traffic light simulator breakout board integrating three 5mm LEDs (Red, Yellow, Green) arranged vertically in a common cathode format. Each LED anode is connected to an individual pin through an onboard 220 Ω series current-limiting resistor, allowing direct connection to microcontroller GPIOs [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**, the module connects directly to safe general-purpose GPIOs (**`GPIO 25 Red`**, **`GPIO 26 Yellow`**, **`GPIO 27 Green`**). At 3.3V drive voltage, the 220 Ω resistors limit current to a safe 5.9 mA to 6.4 mA per LED, well within the ESP32's recommended $\le 12	ext{ mA}$ rating [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Display Configuration** | 3× 5mm LEDs: Red (Top), Yellow (Middle), Green (Bottom) | [E1] |
| **Circuit Configuration** | Common Cathode (All LED cathodes tied to GND) | [E1] |
| **Onboard Current Limiters**| 3× 220 Ω SMD resistors (one per LED channel) | [E1] |
| **Red LED $V_F$ / Current** | 1.8 V to 2.0 V / ~6.4 mA (at 3.3V drive) | [E1/E5] |
| **Yellow LED $V_F$ / Current**| 1.9 V to 2.1 V / ~5.9 mA (at 3.3V drive) | [E1/E5] |
| **Green LED $V_F$ / Current** | 2.1 V to 2.4 V / ~5.0 mA (at 3.3V drive) | [E1/E5] |
| **Total Module Current** | < 18 mA (all three LEDs illuminated simultaneously) | [E5] |
| **Logic Compatibility** | **3.3 V LVTTL Direct Drive** | [E5] |

## Pinout & Wiring

| Pin Label | Function | ESP32 GPIO Connection | Description |
|---|---|---|---|
| **R** | Red LED Anode | **GPIO 25** | Digital Output (High = Illuminated) [E5] |
| **Y** | Yellow LED Anode | **GPIO 26** | Digital Output (High = Illuminated) [E5] |
| **G** | Green LED Anode | **GPIO 27** | Digital Output (High = Illuminated) [E5] |
| **GND** | Common Cathode | **GND** | Connects to common ground reference [E1] |

```
   ESP32-DevKitC V4                      Traffic Light Module
  ┌────────────────┐                     ┌────────────────────┐
  │        GPIO 25 ├────────────────────►│ R (Red LED)        │
  │        GPIO 26 ├────────────────────►│ Y (Yellow LED)     │
  │        GPIO 27 ├────────────────────►│ G (Green LED)      │
  │            GND ├─────────────────────┤ GND                │
  └────────────────┘                     └────────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Standard Municipal Traffic Light Sequence

```cpp
const int redPin = 25;
const int yellowPin = 26;
const int greenPin = 27;

void setup() {
  Serial.begin(115200);
  pinMode(redPin, OUTPUT);
  pinMode(yellowPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  Serial.println("ESP32 Traffic Light Simulator Ready.");
}

void loop() {
  // Phase 1: RED (Stop) - 4 seconds
  digitalWrite(redPin, HIGH);
  digitalWrite(yellowPin, LOW);
  digitalWrite(greenPin, LOW);
  Serial.println("[TRAFFIC] RED -> STOP");
  delay(4000);

  // Phase 2: RED + YELLOW (Prepare to Go) - 1.5 seconds
  digitalWrite(yellowPin, HIGH);
  Serial.println("[TRAFFIC] RED + YELLOW -> PREPARE");
  delay(1500);

  // Phase 3: GREEN (Go) - 4 seconds
  digitalWrite(redPin, LOW);
  digitalWrite(yellowPin, LOW);
  digitalWrite(greenPin, HIGH);
  Serial.println("[TRAFFIC] GREEN -> GO");
  delay(4000);

  // Phase 4: YELLOW (Caution / Prepare to Stop) - 2 seconds
  digitalWrite(greenPin, LOW);
  digitalWrite(yellowPin, HIGH);
  Serial.println("[TRAFFIC] YELLOW -> CAUTION");
  delay(2000);
}
```

## Source References
- Standard Traffic Light Simulation Breakout Technical Overview: https://www.sparkfun.com
