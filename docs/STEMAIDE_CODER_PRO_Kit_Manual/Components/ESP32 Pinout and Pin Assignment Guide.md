# ESP32 Pinout and Master Pin Assignment Guide

## 1. Executive Hardware Scope & Pin Governance

This guide defines the authoritative pin architecture for the **STEMAIDE Coder Pro Kit — ESP32 Edition** based on the **official Espressif ESP32-DevKitC V4** populated with the **ESP32-WROOM-32D module** [E2].

The ESP32-DevKitC V4 exposes 38 physical pins arranged in two 19-pin dual-row headers on a 0.1" (2.54 mm) pitch [E2]. To eliminate the circuit failures, boot loops, flashing errors, and radio contention common in unguided microcontrollers, this guide enforces a strict **Dual-Layer Pin Architecture** [E5].

---

## 2. Pin Categorization & Electrical Rules

Every physical pin on the ESP32-DevKitC V4 is assigned to one of four functional categories [E1/E2]:

```
                                  ESP32-DevKitC V4 Header Pinout
                                         ┌─────────────────┐
               [Power]             3V3  ─┤ 1            38 ├─  GND             [Power]
               [System]             EN  ─┤ 2            37 ├─  GPIO 23         [Safe GPIO - SPI MOSI]
     [ADC1 / Input Only]  GPIO 36 (VP)  ─┤ 3            36 ├─  GPIO 22         [Safe GPIO - I2C SCL]
     [ADC1 / Input Only]  GPIO 39 (VN)  ─┤ 4            35 ├─  GPIO 1          [UART0 TX - Serial Monitor]
     [ADC1 / Input Only]       GPIO 34  ─┤ 5            34 ├─  GPIO 3          [UART0 RX - Serial Monitor]
     [ADC1 / Input Only]       GPIO 35  ─┤ 6            33 ├─  GPIO 21         [Safe GPIO - I2C SDA]
           [Safe / ADC1]       GPIO 32  ─┤ 7            32 ├─  GND             [Power]
           [Safe / ADC1]       GPIO 33  ─┤ 8            31 ├─  GPIO 19         [Safe GPIO - SPI MISO]
             [Safe GPIO]       GPIO 25  ─┤ 9            30 ├─  GPIO 18         [Safe GPIO - SPI SCK]
             [Safe GPIO]       GPIO 26  ─┤ 10           29 ├─  GPIO 5          [Strapping - Reserved]
             [Safe GPIO]       GPIO 27  ─┤ 11           28 ├─  GPIO 17         [Safe GPIO - UART2 TX]
             [Safe GPIO]       GPIO 14  ─┤ 12           27 ├─  GPIO 16         [Safe GPIO - UART2 RX]
             [Strapping]       GPIO 12  ─┤ 13           26 ├─  GPIO 4          [Safe GPIO - SPI SS]
               [Power]             GND  ─┤ 14           25 ├─  GPIO 0          [Strapping - Boot Switch]
             [Safe GPIO]       GPIO 13  ─┤ 15           24 ├─  GPIO 2          [Strapping - Onboard LED]
        [Internal Flash]       GPIO 9   ─┤ 16           23 ├─  GPIO 15         [Strapping - Reserved]
        [Internal Flash]       GPIO 10  ─┤ 17           22 ├─  GPIO 8          [Internal Flash - DO NOT USE]
        [Internal Flash]       GPIO 11  ─┤ 18           21 ├─  GPIO 7          [Internal Flash - DO NOT USE]
               [Power]              5V  ─┤ 19           20 ├─  GPIO 6          [Internal Flash - DO NOT USE]
                                         └─────────────────┘
```

### 2.1 Forbidden Pins: Integrated SPI Flash (`GPIO 6–11`)
- **Pins**: `GPIO 6 (CLK)`, `GPIO 7 (SD0)`, `GPIO 8 (SD1)`, `GPIO 9 (SD2)`, `GPIO 10 (SD3)`, `GPIO 11 (CMD)` [E1].
- **Constraint**: Connected internally inside the WROOM-32D module directly to the 4 MB SPI flash memory chip.
- **Rule**: **STRICTLY FORBIDDEN FOR EXTERNAL CONNECTION.** Connecting any sensor or jumper wire to these pins causes instant program crash, SPI flash corruption, and unrecoverable boot looping [E1].

### 2.2 Boot Strapping Pins (`GPIO 0, 2, 5, 12, 15`)
- **Pins**:
  - `GPIO 0`: Boot mode selector (Must be pulled HIGH internally for normal execution; pulled LOW by EN+BOOT switch during flashing) [E1].
  - `GPIO 2`: Flashing mode & onboard blue LED (Must be left floating or pulled LOW during boot) [E1].
  - `GPIO 5`: SDIO slave timing (Emits high-frequency PWM test signal during boot; must not be pulled LOW at power-up) [E1].
  - `GPIO 12` (MTDI): Flash voltage selection (If pulled HIGH at boot, sets VDD_SDIO to 1.8V, instantly brown-out crashing 3.3V flash memory!) [E1].
  - `GPIO 15` (MTDO): Boot debug output (Controls silent boot logging) [E1].
- **Rule**: **EXCLUDED FROM MASTER INTEGRATION.** No strapping pin may be assigned in the simultaneous master configuration unless isolated by an explicit electrical buffer [E5].

### 2.3 Input-Only Pins (GPI): `GPIO 34, 35, 36 (VP), 39 (VN)`
- **Constraint**: These four pins have **no output drive circuitry** and **no internal pull-up or pull-down resistors** [E1].
- **Rule**: Dedicated exclusively to analog sensors (ADC1) and external active-high digital sensors with dedicated physical pull-down resistors [E5].

### 2.4 Preferred Safe Bidirectional GPIOs
- **Pins**: `GPIO 4, 13, 14, 16, 17, 18, 19, 21, 22, 23, 25, 26, 27, 32, 33` [E1].
- **Features**: Full input and output drive capability, internal programmable pull-up/pull-down resistors, software interrupts, and hardware bus routing [E1/E4].

---

## 3. Dual-Layer Pin Assignment Architecture

### Layer A: Modular / Isolated Lab Assignments
Individual component datasheets in this library provide dedicated pin assignments for standalone lab lessons. This mirrors the educational structure of the original Arduino UNO R3 kit, where pins D2–D13 were reused across separate exercises.

### Layer B: Conflict-Free Simultaneous Master Integration
When assembling advanced multi-peripheral capstone projects, all components are mapped simultaneously to the unified master hardware matrix below.

> [!IMPORTANT]
> **Master Invariant: Zero Strapping Pins Assigned**
> The Master Integration table uses **zero strapping pins** (`GPIO 0, 2, 5, 12, 15`) and **zero flash pins** (`GPIO 6–11`). All analog sensors are mapped strictly to **ADC1 channels** to maintain continuous operation during active Wi-Fi transmission [E1/E5].

### Machine-Checkable Master Allocation Matrix

| ESP32 Pin | Category | Master Peripheral Allocation | Bus / Protocol | Strapping Status | Electrical / Isolation Notes | Evidence |
|---|---|---|---|---|---|---|
| **GPIO 21** | Safe GPIO | LCD 1602 / BME280 / RTC SDA | Hardware I²C | Non-Strapping | Shared I²C bus; bidirectional level shifting for 5V LCD [E3/E5] | [E1/E3] |
| **GPIO 22** | Safe GPIO | LCD 1602 / BME280 / RTC SCL | Hardware I²C | Non-Strapping | Shared I²C bus; bidirectional level shifting for 5V LCD [E3/E5] | [E1/E3] |
| **GPIO 18** | Safe GPIO | RC522 / MAX7219 / 74HC595 SCK | Hardware VSPI | Non-Strapping | Shared SPI clock line [E1/E4] | [E1/E4] |
| **GPIO 19** | Safe GPIO | RC522 MISO | Hardware VSPI | Non-Strapping | SPI Master-In Slave-Out (3.3V native) [E1] | [E1] |
| **GPIO 23** | Safe GPIO | RC522 / MAX7219 / 74HC595 MOSI | Hardware VSPI | Non-Strapping | Shared SPI data out line [E1/E4] | [E1/E4] |
| **GPIO 4** | Safe GPIO | RC522 Chip Select (SS) | Dedicated CS | Non-Strapping | Active-LOW SPI slave select (replaces strapping pin GPIO 5) [E5] | [E1/E5] |
| **GPIO 27** | Safe GPIO | MAX7219 Chip Select (CS) | Dedicated CS | Non-Strapping | Active-LOW SPI slave select (replaces strapping pin GPIO 15) [E5] | [E1/E5] |
| **GPIO 16** | Safe GPIO | HC-05 TXD (ESP32 RX2) | Hardware UART2| Non-Strapping | Direct 3.3V UART receive [E1/E3] | [E1/E3] |
| **GPIO 17** | Safe GPIO | HC-05 RXD (ESP32 TX2) | Hardware UART2| Non-Strapping | Direct 3.3V UART transmit [E1/E3] | [E1/E3] |
| **GPIO 13** | Safe GPIO | HC-SR04 Ultrasonic Trigger | Digital Output| Non-Strapping | 3.3V TTL pulse drives HC-SR04 Trig directly [E1/E5] | [E1/E5] |
| **GPIO 14** | Safe GPIO | HC-SR04 Ultrasonic Echo | Digital Input | Non-Strapping | **Mandatory 1 kΩ / 2 kΩ divider** steps 5V echo to 3.3V [E5] | [E1/E5] |
| **GPIO 25** | Safe GPIO | Stepper ULN2003 IN1 / Relay | Digital Output| Non-Strapping | Driven via 3.3V logic; coils powered by external 5V [E1/E5] | [E1/E5] |
| **GPIO 26** | Safe GPIO | Stepper ULN2003 IN2 / Buzzer | Digital Output| Non-Strapping | Driven via 3.3V logic; coils powered by external 5V [E1/E5] | [E1/E5] |
| **GPIO 32** | Safe GPIO | Stepper ULN2003 IN3 / DS18B20 | Digital Output| Non-Strapping | General-purpose I/O (replaces strapping pin GPIO 12) [E5] | [E1/E5] |
| **GPIO 33** | Safe GPIO | Stepper ULN2003 IN4 | Digital Output| Non-Strapping | General-purpose I/O (replaces strapping pin GPIO 2) [E5] | [E1/E5] |
| **GPIO 34** | Input Only | Potentiometer 10 kΩ | ADC1_CH6 | Non-Strapping | 0–3.3V Analog Input (ADC1, Wi-Fi concurrent) [E1] | [E1/E5] |
| **GPIO 35** | Input Only | Photoresistor LDR | ADC1_CH7 | Non-Strapping | 10 kΩ voltage divider tied to 3.3V rail [E5] | [E1/E5] |
| **GPIO 36 (VP)**| Input Only | XY Joystick X-Axis (VRx) | ADC1_CH0 | Non-Strapping | 0–3.3V Analog Input (ADC1, Wi-Fi concurrent) [E1] | [E1/E5] |
| **GPIO 39 (VN)**| Input Only | XY Joystick Y-Axis (VRy) | ADC1_CH3 | Non-Strapping | 0–3.3V Analog Input (ADC1, Wi-Fi concurrent) [E1] | [E1/E5] |
| **GPIO 0** | Strapping | *UNASSIGNED* | Boot Control | Strapping | Reserved for onboard BOOT pushbutton [E1/E2] | [E1/E2] |
| **GPIO 2** | Strapping | *UNASSIGNED* | Flashing/LED | Strapping | Connected to onboard LED; unassigned to avoid boot conflicts [E1] | [E1/E2] |
| **GPIO 5** | Strapping | *UNASSIGNED* | SDIO Timing | Strapping | Unassigned to prevent boot-time PWM glitches [E1] | [E1/E2] |
| **GPIO 12** | Strapping | *UNASSIGNED* | Flash Voltage | Strapping | Unassigned to prevent 1.8V flash brownout corruption [E1] | [E1/E2] |
| **GPIO 15** | Strapping | *UNASSIGNED* | Boot Output | Strapping | Unassigned to isolate boot logging [E1] | [E1/E2] |
| **GPIO 6–11** | Forbidden | *UNASSIGNED* | SPI Flash Bus | Forbidden | Internal flash memory; strictly unassigned [E1] | [E1] |

> [!NOTE]
> **Expansion Capacity Reservation Invariant**:
> Unused GPIOs are intentionally reserved as expansion capacity for student capstone scaling and shall not be automatically assigned merely to increase utilization [E5].

---

## 4. Breadboard Alignment & Physical Clearance (830-Point)

Standard 830-point solderless breadboards feature two 5-hole terminal strip arrays (Columns A–E on the left and F–J on the right) separated by a 0.3" (7.62 mm) center ravine [E5].

```
                               Breadboard 830 Seating Diagram
  (+) Power Rail ───────────────────────────────────────────────────────────── (5V External)
  (−) Power Rail ───────────────────────────────────────────────────────────── (GND Common)
        Column:   A     B     C     D     E    ║    F     G     H     I     J
                 [ ]   [●]───[─]───[─]───[─]   ║   [─]───[─]───[─]───[●]   [ ]
                  │     │                      ║                      │     │
                  │     └─ ESP32 Left Header   ║  ESP32 Right Header ─┘     │
                  │                            ║                            │
                  └── Accessible Tie-Point Row ║ Accessible Tie-Point Row ──┘
                      for Student Jumpers      ║ for Student Jumpers
```

- When the ESP32-DevKitC V4 is inserted straddling the center ravine, its left pin row seats into Column B and its right pin row seats into Column I [E5/E6].
- This configuration leaves **Column A completely open and accessible on the left side**, and **Column J completely open and accessible on the right side** along the entire 19-row length of the board [E5/E6].
- Students insert jumper wires directly into Column A and Column J to access every pin on the board without needing an expansion breakout shield [E6].
