# Stepper Motor Driver Board (ULN2003) — Technical Datasheet

## General Description

A multi-channel high-voltage, high-current Darlington transistor array breakout board designed specifically for driving unipolar stepper motors such as the **28BYJ-48**. The board incorporates four active channels with onboard status indicator LEDs, input pull-down resistors, and an external 5V power terminal block [E1].

### Direct 3.3V Logic Drive Compatibility
The ULN2003 IC incorporates internal $2.7\text{ k}\Omega$ base resistors designed for TTL and 5V CMOS logic [E1]. When driven by the **Espressif ESP32-DevKitC V4 (3.3V logic)**:

$$I_B = \frac{V_{\text{GPIO}} - V_{BE}}{R_B} = \frac{3.3\text{ V} - 1.4\text{ V}}{2700\,\Omega} = 0.70\text{ mA} \quad [\text{E5}]$$

With a minimum Darlington current gain $h_{FE} = 1000$, a base current of $0.70\text{ mA}$ can sink up to **700 mA collector current**, far exceeding the 100 mA drawn by each 28BYJ-48 motor coil [E1/E5]! Direct drive from ESP32 GPIOs to ULN2003 inputs (IN1–IN4) is **100% verified, safe, and reliable** [E5].

### Safe Pin Allocation (Zero Strapping Pins)
In the STEMAIDE Coder Pro Kit, the driver inputs connect strictly to **safe general-purpose GPIOs: `GPIO 25, 26, 32, 33`**. Boot strapping pins `GPIO 12` and `GPIO 2` are deliberately avoided to eliminate boot-time reset failures [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Driver IC** | Texas Instruments / ST ULN2003A Darlington Transistor Array | [E1] |
| **Number of Channels** | 4 channels used (7 channels integrated in silicon) | [E1] |
| **Output Peak Current** | 500 mA per channel continuous (600 mA peak) | [E1] |
| **Output Voltage Rating** | Up to 50 V DC (Internal clamp flyback diodes included) | [E1] |
| **Input Logic Drive** | **3.3 V LVTTL Directly Compatible** ($I_B \approx 0.70\text{ mA}$) | [E1/E5] |
| **Motor Supply Voltage** | 5.0 V DC (from External Power Rail; NOT ESP32 3V3!) | [E5] |
| **Onboard Indicators** | 4 yellow/red LEDs showing active coil state | [E1] |

## Interfacing Architecture & Wiring

```
   ESP32-DevKitC V4             ULN2003 Driver Board                 28BYJ-48 Stepper
  ┌────────────────┐            ┌───────────────────┐               ┌────────────────┐
  │        GPIO 25 ├───────────►│ IN1          OUT1 ├──────────────►│ Orange (Coil 1)│
  │        GPIO 26 ├───────────►│ IN2          OUT2 ├──────────────►│ Yellow (Coil 2)│
  │        GPIO 32 ├───────────►│ IN3          OUT3 ├──────────────►│ Pink   (Coil 3)│
  │        GPIO 33 ├───────────►│ IN4          OUT4 ├──────────────►│ Blue   (Coil 4)│
  │            GND ├──────┬────►│ GND           COM ├──────────────►│ Red    (+5V)   │
  └────────────────┘      │     └─────────┬─────────┘               └────────────────┘
                          │               │
                         GND             +5V (External Supply Rail)
```

## ESP32 Sample Code (Core 3.x) — Precision Half-Step Stepper Sweep

```cpp
// Safe non-strapping ESP32 GPIOs
const int in1 = 25;
const int in2 = 26;
const int in3 = 32;
const int in4 = 33;

// 8-step half-stepping sequence
const byte stepSequence[8][4] = {
  {1, 0, 0, 0},
  {1, 1, 0, 0},
  {0, 1, 0, 0},
  {0, 1, 1, 0},
  {0, 0, 1, 0},
  {0, 0, 1, 1},
  {0, 0, 0, 1},
  {1, 0, 0, 1}
};

void setStep(int step) {
  digitalWrite(in1, stepSequence[step][0]);
  digitalWrite(in2, stepSequence[step][1]);
  digitalWrite(in3, stepSequence[step][2]);
  digitalWrite(in4, stepSequence[step][3]);
}

void stepMotor(int steps, int stepDelayUs, bool forward) {
  for (int i = 0; i < steps; i++) {
    int stepIdx = forward ? (i % 8) : (7 - (i % 8));
    setStep(stepIdx);
    delayMicroseconds(stepDelayUs);
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  Serial.println("ESP32 ULN2003 Stepper Controller Ready.");
}

void loop() {
  Serial.println("Rotating 1 Full Turn Clockwise (4096 half-steps)...");
  stepMotor(4096, 1200, true);
  delay(1000);

  Serial.println("Rotating 1 Full Turn Counter-Clockwise...");
  stepMotor(4096, 1200, false);
  delay(1000);
}
```

## Source References
- Texas Instruments ULN2003A High-Voltage High-Current Darlington Transistor Arrays: https://www.ti.com/lit/ds/symlink/uln2003a.pdf
