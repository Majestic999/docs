# Breadboard 830-Point — Technical Datasheet

## General Description

A standard 830 tie-point solderless prototyping breadboard designed for temporary electronic circuit assembly. The breadboard features 630 tie points in the main IC circuit area (arranged in two sets of 5-hole rows separated by a central 0.3" DIP divider ravine) and 200 tie points in four distribution power bus strips [E1].

When used with the **Espressif ESP32-DevKitC V4 (38-pin dual-row header format)**, the board straddles the central ravine, seating into row columns B and I. This geometry preserves **exactly 1 accessible tie-point column on each side** (Column A on the left and Column J on the right), allowing students to plug jumper wires directly into every pin [E5/E6].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Total Tie Points** | 830 points | [E1] |
| **IC Circuit Area** | 630 tie points (63 columns × 2 sides × 5 contact holes) | [E1] |
| **Power Distribution Rails** | 4 bus strips (2 top, 2 bottom; 50 points per strip = 200 points) | [E1] |
| **Hole Pitch / Spacing** | 2.54 mm (0.1") standard DIP grid | [E1] |
| **Center Ravine Width** | 7.62 mm (0.3") standard IC width | [E1] |
| **Contact Material** | Nickel-plated phosphor bronze spring clips | [E1] |
| **Insertion Life** | $\ge 50,000$ insertion cycles | [E1] |
| **Max Voltage Rating** | 36 V DC | [E1] |
| **Max Current per Point** | 2.0 A continuous | [E1] |
| **Recommended Wire Gauge** | 22 AWG to 26 AWG solid copper wire | [E1] |
| **Dimensions** | 165 × 55 × 10 mm | [E1] |

## Internal Layout & ESP32 Seating Architecture

```
  (+) External 5V Power Rail ──────────────────────────────────────────────────────────
  (−) Common GND Power Rail ───────────────────────────────────────────────────────────
      Row Col:  A     B     C     D     E    ║    F     G     H     I     J
               [ ]   [●]───[─]───[─]───[─]   ║   [─]───[─]───[─]───[●]   [ ]
                │     │                      ║                      │     │
                │     └─ ESP32 Left Header   ║  ESP32 Right Header ─┘     │
                │        (Pins 1 to 19)      ║        (Pins 20 to 38)     │
                │                            ║                            │
                └── Student Jumper Access    ║ Student Jumper Access ─────┘
                    (Column A)               ║ (Column J)
```

## Power Bus Strip Configuration
- **Upper Red Rail (+)**: Connect to External 5V regulated power supply (powers motors, servos, relays, and LCD backlight) [E5].
- **Upper Blue Rail (−)**: Connect to Common GND [E5].
- **Lower Red Rail (+)**: Connect to ESP32 3V3 rail (powers low-power 3.3V sensors only) [E5].
- **Lower Blue Rail (−)**: Connect to Common GND (must be bridged to Upper Blue Rail to establish common ground reference) [E5].

## Source References
- Global Specialties Solderless Breadboard Technical Reference: https://globalspecialties.com
