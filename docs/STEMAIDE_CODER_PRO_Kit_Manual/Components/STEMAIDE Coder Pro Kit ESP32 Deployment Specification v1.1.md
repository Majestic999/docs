# STEMAIDE Coder Pro Kit: ESP32 Deployment Specification v1.1
**Document Identifier:** STEMAIDE-SPEC-ESP32-PRO-V1.1  
**Maturity Status:** Deployment-Engineering Ready (Pending D1–D10 Physical Gates)  
**Parent Authority:** `raw/Rulings.md` → `wiki/CONSTITUTION.md` → This Specification  
**Target Architecture:** Espressif ESP32-DevKitC V4 (ESP32-WROOM-32D / WROOM-32E)  
**Target Software:** ESP32 Arduino Core 3.x (Clean API Standard)  
**Date of Release:** September 2026  

---

## 0. Epistemic Claim Taxonomy

To ensure deployment integrity and prevent laboratory recommendations from being misclassified as empirically verified hardware behaviors, all assertions in this specification carry epistemic classifications:

- **`[EF]` Verified Electrical Fact:** Confirmed through Espressif technical reference manuals, silicon errata, official schematics, or laboratory measurements.
- **`[DR]` Design Requirement:** Non-negotiable architectural constraint mandated for kit safety, concurrency, or pedagogical consistency.
- **`[CP]` Recommended Classroom Practice:** Pedagogical, procedural, or operational convention recommended for primary/secondary classroom environments.
- **`[PV]` Physical Validation Required:** Specific parameter or condition that cannot be proven by software or datasheet inspection alone; must be verified on each physical receiving batch or bench test fixture.

---

## 1. Power System Topology & Rail Isolation Standard

### 1.1 Mutual Exclusivity Invariant `[EF]` `[DR]`
Espressif explicitly documents that the ESP32-DevKitC V4 supports three mutually exclusive power methods:
1. Micro-USB port (Nominal 5.0 V input, regulated onboard by AMS1117-3.3 / ME6211 to 3.3 V).
2. 5V / VIN pin (External regulated 5.0 V input to onboard LDO).
3. 3V3 / GND pins (Direct 3.3 V regulated rail input, bypassing onboard LDO).

**Mandatory Rule:** Do NOT connect more than one power source simultaneously. Connecting an external power supply to the DevKitC 5V/VIN pin while Micro-USB is plugged in risks back-feeding current into host PC USB controllers or damaging the onboard LDO reverse diode.

### 1.2 Classroom Actuator Power Topology `[DR]` `[CP]`
External inductive, electro-mechanical, and high-current loads (SG90 servos, 28BYJ-48 stepper motors, electromechanical relays, high-power DC motors) must NEVER draw current from the ESP32 DevKitC 3.3 V logic pin or the onboard USB 5V pin.

```
                 ┌──────────────────────────┐
                 │ Classroom PC / Laptop    │
                 └────────────┬─────────────┘
                              │ Micro-USB Cable (Logic Power & Flash)
                              ▼
                     ┌────────────────┐
                     │ ESP32 DevKitC  │
                     │   3.3 V logic  │
                     └───────┬────────┘
                             │
              COMMON GROUND  │ (Zero Volts Reference Only)
                             │
                     ┌───────▼────────┐
                     │ Regulated 5 V  │
                     │ ≥ 2 A actuator │
                     │ power supply   │
                     └───┬────┬───┬───┘
                         │    │   │ (Positive 5V rail dedicated to loads)
                       Servo Stepper Relay/Motor
```

> **Normative Electrical Requirement:**  
> **The external 5 V rail powers external loads, not the ESP32 board. Do not connect the external 5 V rail to the DevKitC 5V/VIN pin while USB is connected.**  
> The ESP32 DevKitC and external actuator supply MUST share a common Ground (GND) connection to establish an equipotential logic reference, but the positive voltage rails (+3.3V, USB +5V, External +5V) MUST REMAIN STRICTLY ISOLATED.

---

## 2. Pin Architecture & Wi-Fi Concurrency

### 2.1 Concurrency Rule for ADC1 vs ADC2 `[EF]` `[DR]`
- **The SAR ADC2 peripheral is hardware-shared with the 2.4 GHz Wi-Fi / Bluetooth baseband.** When Wi-Fi is active (STA, AP, or ESP-NOW mode), calls to `analogRead()` on ADC2 pins (`GPIO 0, 2, 4, 12, 13, 14, 15, 25, 26, 27`) fail, return indeterminate garbage, or trigger DMA conflicts.
- **Normative Rule:** All analog sensor inputs across all projects are allocated EXCLUSIVELY to **ADC1 channels** (`GPIO 32, 33, 34, 35, 36 [VP], 39 [VN]`).
- **Clarification on Digital Pins:** Using `GPIO 25, 26, 27` as digital actuator outputs (e.g., motor drivers, LEDs, servos) is 100% valid during active Wi-Fi. Their internal ADC2 multiplexers remain inactive when configured via `pinMode(pin, OUTPUT)`.
- **Input-Only Pins:** `GPIO 34, 35, 36, 39` lack internal software pull-up or pull-down resistors `[EF]`. External pull-ups/pull-downs are mandatory when connecting resistive dividers, buttons, or open-drain lines to these pins.

### 2.2 Strapping Pin Elimination `[EF]` `[DR]`
Pins `GPIO 0, 2, 5, 12 (MTDI), 15 (MTDO)` control internal boot modes, SPI flash voltage (1.8V vs 3.3V), and ROM bootloader execution during power-on reset. To eliminate classroom flash failures:
- In the **Simultaneous Master Integration**, strapping pins are strictly unassigned.
- In modular standalone labs, if `GPIO 0` or `GPIO 2` is used, pull-up/pull-down states must be guaranteed at boot.

---

## 3. Hardware Lifecycle, Board Identity & Receiving Acceptance

### 3.1 3-Class Hardware Matrix `[DR]`

| Class | Hardware Identity | Module Status | Role in STEMAIDE Program | Curriculum & Driver Implications |
| :--- | :--- | :--- | :--- | :--- |
| **B0** | ESP32-DevKitC V4 + ESP32-WROOM-32D | NRND (Espressif) | **Normative Baseline** | Preserves Bluetooth Classic SPP for legacy HC-05 Android apps. CP2102N / CH340 bridge. |
| **B1** | ESP32-DevKitC V4 + ESP32-WROOM-32E | Active Production | **Compatibility Successor** | Drop-in pin & electrical compatible. Fixes ECO bugs. Bluetooth Classic retained. |
| **B2** | ESP32-S3-DevKitC-1 + ESP32-S3-WROOM-1 | Active Production | **Architectural Successor** | Native USB-OTG, dual USB-C, vector instructions, BLE 5.0. **Omits Bluetooth Classic SPP**. |

### 3.2 Receiving Acceptance Procedure (`BOARD_ACCEPTANCE`) `[PV]`
Because clone manufacturers frequently relabel generic ESP32 boards or substitute substandard flash/LDO components, every procurement batch delivered to STEMAIDE Africa must pass the physical **Gate D2 Board Identity Register**:

```
========================================================================
STEMAIDE ESP32 BOARD RECEIVING & IDENTITY ACCEPTANCE CHECKLIST (GATE D2)
========================================================================
Batch / PO Number:         ____________________
Receiving Inspector:       ____________________  Date: _______________

[ ] 1. Silk-Screen Verification:
       - Board markings state "ESP32-DevKitC V4" or authorized OEM variant.
       - Pinout matches official 38-pin dual-row standard (19 pins per side).
[ ] 2. RF Shield Laser-Etch Inspection:
       - Manufacturer: Espressif Systems (or authorized licensed module).
       - Module ID clearly marked: "ESP32-WROOM-32D" (B0) or "ESP32-WROOM-32E" (B1).
       - FCC ID / CE / Anatel regulatory marks present.
[ ] 3. USB-to-UART Bridge Identification:
       - Silicon: Silicon Labs CP2102N (QFN-28) OR WCH CH340C / CH340G.
       - Driver profile matched to Classroom Golden Image.
[ ] 4. Voltage Regulator (LDO) Rating:
       - Marking indicates AMS1117-3.3, ME6211, or RT9080 (≥ 500 mA rating).
       - Visual solder joint inspection: No solder bridges or flux contamination.
[ ] 5. Breadboard Fitment & Mechanical Clearance:
       - Fits standard 830-point breadboard leaving minimum 1 row exposed per side.
[ ] 6. Bootloader & Flash Auto-Program Test:
       - Board enters download mode via DTR/RTS auto-reset circuit without pressing BOOT button.
       - Flash capacity confirmed: 4 MB (32 Mbit) Quad-SPI.

DISPOSITION:  [ ] ACCEPTED FOR CLASSROOM DEPLOYMENT    [ ] REJECTED (QUARANTINE)
Inspector Signature: __________________________________________________
```

---

## 4. Multi-Level Bill of Materials & Consumables Reserve

### 4.1 Level 1: Core Upgrade Pack (Existing UNO R3 Kit Conversion)
Converts one STEMAIDE Coder Pro Kit from Arduino UNO R3 to ESP32:

| Ref | Item Description | Qty | Target Specification | Electrical Safety Tier |
| :--- | :--- | :--- | :--- | :--- |
| U-01 | ESP32 Development Board | 1 | ESP32-DevKitC V4 (WROOM-32D or 32E), 38-pin | Green (3.3V Logic) |
| U-02 | USB Interface Cable | 1 | USB Type-A to Micro-B (or Type-C per bridge), 1.0 m, 24/28 AWG | Green |
| U-03 | Resistor Divider Pack | 10 | $1.0	ext{ k}\Omega \pm 1\%$ and $2.0	ext{ k}\Omega \pm 1\%$, 0.25 W Metal Film | Red (Signal Protection) |
| U-04 | Logic Level Shifter Module | 2 | 4-channel bidirectional BSS138 MOSFET board | Red (Signal Protection) |
| U-05 | Bus Buffer IC | 1 | 74HCT125 Quad Non-Inverting Buffer (DIP-14) | Red (Signal Protection) |
| U-06 | Auxiliary Actuator Rail Board | 1 | Dual-rail breadboard power module with common GND terminal | Yellow (External Power) |

### 4.2 Level 2: Full Production Kit (New Turnkey Kits)
Includes all 42 legacy components + Level 1 upgrade pack + upgraded 5V 2A external DC power adapter.

### 4.3 Level 3: Classroom Consumables & Failure Spares BOM `[DR]`
In a 30-student lab (15 workstations of 2 students), hardware wears out and fails due to wiring errors. Procurement must enforce minimum spare ratios:

| Component Category | Minimum Spare Ratio | Quantity for 15 Workstations | Primary Failure Mode Addressed |
| :--- | :--- | :--- | :--- |
| **ESP32 MCU Boards** | $\ge 10\%$ | 2 spare boards | Over-voltage on GPIO, fractured Micro-USB connector |
| **USB Data Cables** | $\ge 10\%$ | 2 spare cables | Internal conductor fatigue, broken Micro-B teeth |
| **BSS138 Level Shifters**| $\ge 15\%$ | 3 spare modules | Reversed HV/LV connections, electrostatic discharge |
| **Resistors ($1	ext{k}, 2	ext{k}, 220\Omega$)** | $\ge 100\%$ | 50 spare resistors | Bent leads, lost components |
| **SG90 Micro Servos** | $\ge 10\%$ | 2 spare servos | Stripped nylon gears, motor driver burn-out |
| **28BYJ-48 Steppers + ULN2003**| $\ge 10\%$ | 2 spare sets | Overheated Darlington arrays |
| **Solderless Breadboards** | $\ge 10\%$ | 2 spare boards | Deformed spring contacts from oversized probes |
| **Dupont Jumpers (M-M, M-F)** | $\ge 30\%$ | 60 spare wires | Broken internal strands, loose crimp housings |

---

## 5. Physical Interface Standard: 3-Tier Color-Coded Governance

To turn electrical safety from a passive reading exercise into an active physical constraint, all kit modules, breadboard rails, and cables follow a strict three-tier physical interface standard:

```
========================================================================
STEMAIDE PHYSICAL INTERFACE & CABLING STANDARD
========================================================================

   [ GREEN ]   3.3 V NATIVE LOGIC RAIL & SENSORS
   ─────────   ● Voltage: 3.3 V DC strictly
               ● Wires: Green, Blue, White jumpers
               ● Connection: Direct to ESP32 3V3 pin and ADC1/GPIO pins
               ● Modules: Potentiometer, Photocell, DHT11, Touch, PIR

   [ YELLOW ]  5.0 V ACTUATOR POWER RAIL & SHARED GROUND
   ──────────  ● Voltage: 5.0 V DC external supply strictly (≥ 2 A)
               ● Wires: Red (External +5V), Black (Common GND)
               ● Connection: External power adapter rail; NEVER to ESP32 VIN
               ● Modules: Servo VCC, Stepper Driver VCC, Relay Coil VCC

   [ RED ]     PROTECTED SIGNAL INTERFACE (SHIFTER / BUFFER / DIVIDER)
   ───────     ● Voltage: 5.0 V logic transitioning to 3.3 V ESP32 input
               ● Wires: Orange, Yellow, Purple jumpers
               ● Hardware: Voltage divider, BSS138 shifter, 74HCT125 buffer
               ● Modules: HC-SR04 Echo, I2C 1602 LCD, MAX7219, Relay IN
========================================================================
```

**Physical Implementation in Kits:**
1. Workstation breadboards carry colored adhesive tape along the power rails (Green for 3.3V logic side, Yellow for 5V actuator side).
2. Level shifters and divider packs are pre-packaged in Red anti-static pouches labeled: `CAUTION: MANDATORY LEVEL SHIFTER FOR 5V SENSORS`.

---

## 6. Diagnostic Protocol & Bench Acceptance Fixture

The diagnostic software has been redesigned from a mere software initialization check into a rigorous **Dual-Mode Verification Suite**.

### 6.1 Mode A: Production Bench Acceptance Test (Fixture-Guided) `[PV]`
Requires connecting a physical testing jig before running:
1. **UART Loopback Fixture:** External jumper between `GPIO 17 (TX2)` and `GPIO 16 (RX2)`.
2. **I2C Reference Test:** Known reference device (e.g., PCF8574 LCD at address `0x27`) connected to `GPIO 21/22`.
3. **ADC Calibrated Fixture:** Two known reference voltage points:
   - Point 0: Common GND (0.0 V).
   - Point 1: 3.3 V rail via $10	ext{ k}\Omega$ precision reference.
4. **RF Over-the-Air Verification:** Active Wi-Fi scan requiring detection of at least one classroom access point with RSSI measurement $\ge -80	ext{ dBm}$.

### 6.2 Mode B: Standalone Software Smoke Test (Field Inspection)
When run without the external bench fixture:
- Performs internal peripheral checks.
- Scans I2C bus and reports exact device count.
- Reads internal hall effect / ADC channels and tags reading as uncalibrated.
- Tests Wi-Fi MAC retrieval, NVS non-volatile storage, and Bluetooth controller initialization.
- **Terminal Status Qualification:** Outputs `DIAGNOSTIC STATUS: SOFTWARE-ACCESSIBLE SUBSYSTEMS PASSED`. It is strictly forbidden to claim "HARDWARE FULLY OPERATIONAL" without Mode A fixture testing.

---

## 7. Ten-Gate Deployment Certification Architecture (D1–D10)

The release of the STEMAIDE Coder Pro Kit ESP32 Edition is governed by 10 auditable gates:

```
[ D1 ] Physical BOM & Spares Inventory Complete
   │   └─ Artifact: Signed BOM & Consumables Inspection Checklist
   ▼
[ D2 ] Board Identity & Receiving Control
   │   └─ Artifact: Module Marking & Physical Identity Register
   ▼
[ D3 ] Power System & Rail Isolation Acceptance
   │   └─ Artifact: Multimeter Rail Isolation & Reverse-Current Test Report
   ▼
[ D4 ] Physical Wiring & Color Standard Acceptance
   │   └─ Artifact: Breadboard & Cable Harnessing Inspection Sign-Off
   ▼
[ D5 ] Diagnostic Firmware & Bench Acceptance
   │   └─ Artifact: Mode A / Mode B Diagnostic Suite Serial Logs
   ▼
[ D6 ] Classroom Golden Image Verification
   │   └─ Artifact: Standalone Offline Arduino IDE Package & SHA-256 Checksum
   ▼
[ D7 ] 160-Project Curriculum Migration Audit
   │   └─ Artifact: Machine-Auditable Project Migration Matrix (100% Preserved)
   ▼
[ D8 ] IoT & Embedded Wireless Security Audit
   │   └─ Artifact: Track E Security & Secrets-Management Review
   ▼
[ D9 ] Ghanaian Classroom Field Pilot
   │   └─ Artifact: 30-Student Lab Pilot Report & Teacher Usability Feedback
   ▼
[ D10] Formal Production Release Certification
       └─ Artifact: Signed Master Certificate of Deployment Readiness
```

---

## 8. Curriculum Architecture & The Five Superpower Tracks

The curriculum maintains strict pedagogical continuity: students complete foundational sequential programming before progressing to asynchronous, networked, and real-time paradigms:

```
LEVEL 0: FOUNDATIONAL SEQUENTIAL ELECTRONICS (160 Migrated Lessons)
  ├─ Beginner: Digital I/O, Analog Input (ADC1), Sound, Basic Displays
  ├─ Intermediate: Ultrasonic, Servo, PWM Control, Active Sensor Modules
  ├─ Advance: System Integration, Multi-Sensor Arrays, Complex Actuation
  └─ Expert I–III: Industrial Automation, Robotics, Display Graphics
                            │
                            ▼
LEVEL 1: WIRELESS FOUNDATION (Local Connectivity)
  ├─ Track A: Local HTTP Web Server & Interactive Dashboard (LAN Control)
  └─ Track B: ESP-NOW Peer-to-Peer Low-Latency Instrument Mesh
                            │
                            ▼
LEVEL 2: EDGE & CLOUD INTEGRATION (Distributed Systems)
  └─ Track C: Industrial MQTT Telemetry & Cloud Dashboards
                            │
                            ▼
LEVEL 3: REAL-TIME SYSTEMS (Deterministic Execution)
  └─ Track D: FreeRTOS Preemptive Multi-Tasking, Queues, Semaphores & Mutexes
                            │
                            ▼
LEVEL 4: EMBEDDED CYBER-SECURITY (Safe & Responsible IoT)
  └─ Track E: Device Identity, TLS Encryption, Credentials Management & Safe Actuation
```

### 8.1 Track E: Embedded Security & Responsible IoT `[DR]`
Networked microcontrollers introduce vulnerabilities absent in standalone Arduino boards. Track E provides mandatory instruction in:
1. **Device Identity & MAC Governance:** Why MAC addresses are not passwords; spoofing risks.
2. **Secrets Segregation:** Keeping Wi-Fi SSIDs, passwords, and API tokens out of Git repositories and public source code via `secrets.h` and NVS flash partitioning.
3. **Transport Security:** Concepts of TLS/HTTPS versus unencrypted HTTP/MQTT on public school Wi-Fi networks.
4. **Physical Actuation Safety over Network:** Implementing heartbeats, timeouts, and failsafe states so that lost Wi-Fi connections do not leave motors running or heaters energized.
