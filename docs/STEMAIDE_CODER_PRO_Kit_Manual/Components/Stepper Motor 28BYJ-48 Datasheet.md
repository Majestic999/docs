# Stepper Motor (28BYJ-48) — Technical Datasheet

## General Description

The 28BYJ-48 is a 5-wire unipolar 4-phase permanent-magnet reduction stepper motor. It features an integrated internal planetary gear reduction gearbox with a nominal ratio of 64:1, providing high positioning torque, fine angular resolution, and precise motion control [E1].

### Mandatory Power Isolation Rule
> **The 28BYJ-48 stepper motor coils draw up to 240 mA peak at 5V. The motor must NEVER be powered from the ESP32 3V3 rail! The motor power lead (Red wire) must connect directly to the external 5V power supply rail with common ground** [E1/E5].

The motor coils are driven sequentially using the companion **ULN2003 Darlington driver board**, whose inputs connect directly to the ESP32's 3.3V GPIOs [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Motor Type** | 4-phase, 5-wire unipolar permanent magnet stepper motor | [E1] |
| **Rated Voltage** | 5.0 V DC | [E1] |
| **Coil Resistance** | 50 Ω ±7% per coil winding (at 25 °C) | [E1] |
| **Gear Reduction Ratio**| 64:1 nominal (Exact: 63.68395:1 / 4075.772 steps/rev in half-step) | [E1] |
| **Stride Angle** | 5.625° / 64 = 0.08789° per step in half-step mode | [E1] |
| **Steps per Revolution**| 2048 steps (Full-step mode); 4096 steps (Half-step mode) | [E1] |
| **Current per Phase** | ~100 mA at 5V DC ($I = \frac{5.0\text{V}}{50\,\Omega}$) | [E1/E5] |
| **Pull-in Torque** | $\ge 34.3\text{ mN}\cdot\text{m}$ (300 gf·cm at 100 Hz) | [E1] |
| **Detent Torque** | $\ge 29.4\text{ mN}\cdot\text{m}$ | [E1] |

## Internal Coil Schematic & Wire Colours

```
                  +5V Common Supply (Red Wire)
                              │
             ┌────────┬───────┴────────┬────────┐
             │        │                │        │
           Coil 1   Coil 2           Coil 3   Coil 4
          (Orange) (Yellow)          (Pink)   (Blue)
             │        │                │        │
             ▼        ▼                ▼        ▼
            ULN2003 Driver Board Outputs (OUT1 to OUT4)
```

## 4-Phase Drive Sequences

| Mode | Phase 1 (Orange) | Phase 2 (Yellow) | Phase 3 (Pink) | Phase 4 (Blue) | Steps / Rev |
|---|---|---|---|---|---|
| **Full-Step (4-Step)** | 1, 1, 0, 0 | 0, 1, 1, 0 | 0, 0, 1, 1 | 1, 0, 0, 1 | 2048 |
| **Half-Step (8-Step)** | 1, 1, 0, 0, 0, 0, 0, 1 | 0, 1, 1, 1, 0, 0, 0, 0 | 0, 0, 0, 1, 1, 1, 0, 0 | 0, 0, 0, 0, 0, 1, 1, 1 | 4096 |

## Source References
- Changzhou Chuangwei Motor 28BYJ-48 Stepper Motor Specifications: http://www.cz-motor.com
