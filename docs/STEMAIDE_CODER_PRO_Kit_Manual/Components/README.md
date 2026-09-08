# STEMAIDE Coder Pro Kit — ESP32 Components Reference Library

## 1. Executive Summary & Architecture Overview

This library contains the official technical datasheets, schematics, pinout guides, and software specifications for the **STEMAIDE Coder Pro Kit — ESP32 Edition**. It establishes a complete, production-grade migration from the legacy 8-bit, 5V Arduino UNO R3 (ATmega328P) architecture to a high-performance 32-bit, 3.3V Espressif ESP32 embedded platform.

The Coder Pro Kit is designed for hands-on, project-based STEM education across Africa and emerging maker ecosystems. Migrating to the ESP32 platform preserves all 44 original sensor, actuator, and interface modules while expanding pedagogical scope into modern Internet of Things (IoT), cloud telemetry, dual-core real-time multitasking (FreeRTOS), high-resolution signal synthesis, and native wireless communications.

### Key Architectural Shifts

| Architectural Dimension | Legacy Arduino UNO R3 | STEMAIDE ESP32 Edition | Educational & Engineering Advantage | Evidence Tier |
|---|---|---|---|---|
| **Microcontroller Core** | 8-bit ATmega328P (AVR RISC) @ 16 MHz | 32-bit Dual-Core Tensilica Xtensa LX6 @ 240 MHz | 15× clock speed; real-time dual-core parallel processing | [E1] |
| **System Memory** | 2 KB SRAM, 32 KB Flash, 1 KB EEPROM | 520 KB SRAM, 4 MB Quad-SPI Flash | Enables complex networking stacks, JSON parsing, FreeRTOS | [E1] |
| **I/O Logic Level** | 5.0 V TTL / CMOS | **3.3 V LVTTL (Not 5V Tolerant)** | Direct compatibility with modern 3.3V digital sensors | [E1] |
| **Analog-to-Digital Converter** | 10-bit resolution (0–1023), 6 channels | 12-bit resolution (0–4095), 18 channels | 4× finer voltage quantization; higher dynamic range | [E1] |
| **Pulse-Width Modulation** | 6 fixed timer channels, 8-bit (490/980 Hz) | 16 LEDC channels, 1–16 bit configurable (up to 40 MHz) | Glitch-free multi-servo, motor, and audio synthesis | [E1/E4] |
| **Serial Communication** | 1 Hardware UART + Bit-Banged `SoftwareSerial` | 3 Hardware UARTs (UART0, UART1, UART2) | Eliminates fragile, timing-sensitive software serial | [E1] |
| **Integrated Wireless** | None (requires external shields/modules) | 2.4 GHz Wi-Fi (802.11 b/g/n) + Bluetooth v4.2 BR/EDR & BLE | Enables IoT dashboards, MQTT telemetry, smartphone control | [E1] |

---

## 2. Normative Hardware Specification

To ensure exact reproducibility across school labs and fabrication runs, this adaptation defines a strict normative hardware standard:

- **Normative Board Target**: **Official Espressif ESP32-DevKitC V4** populated specifically with the **ESP32-WROOM-32D module** [E2].
- **Silicon Target**: Espressif ESP32-D0WD-V3 (chip revision v3.0 / ECO v3), Dual-Core Xtensa LX6 @ 240 MHz [E1].
- **Flash Memory**: 4 MB (32 Mbit) Quad-SPI external flash connected to internal pins GPIO 6–11 [E1].
- **USB Interface**: USB 2.0 Type-B Micro (Micro-USB) receptacle interfaced via a Silicon Labs CP2102N USB-to-UART bridge [E2].
- **Form Factor**: 38-pin dual-row development board (0.1" / 2.54 mm pitch; pin row spacing 25.4 mm / 1.0" to 27.9 mm / 1.1") [E2].
- **Breadboard Compatibility**: Fits standard 830-point solderless breadboards across the center IC dividing trough, leaving exactly 1 tie-point column accessible on each side (column A on the left, column J on the right) [E5/E6].

> [!IMPORTANT]
> **Normative Baseline Invariant**:
> Only the official Espressif ESP32-DevKitC V4 populated with ESP32-WROOM-32D is normative. Third-party boards marketed under similar generic names (e.g., "NodeMCU-32S", "DOIT ESP32 DevKit V1") vary in pinouts, LDO regulators, and USB bridges and must not be assumed electrically identical without schematic audit [E2/E7].

---

## 3. Module Lifecycle & NRND Management

Espressif Systems has officially designated the `ESP32-WROOM-32D` module as **"Not Recommended for New Designs" (NRND)** in favor of newer revisions such as the `ESP32-WROOM-32E` and `ESP32-S3` series [E1].

### Lifecycle Policy & Migration Roadmap
1. **Rationale for WROOM-32D Baseline**: The STEMAIDE Coder Pro Kit includes the HC-05 Bluetooth module and associated classroom curricula for Bluetooth Serial Port Profile (SPP) communication. The ESP32-WROOM-32D incorporates dual-mode Bluetooth (Classic BR/EDR + BLE), providing native hardware SPP parity with HC-05. Modern successor chips (such as ESP32-S3 and ESP32-C6) support BLE only and omit Bluetooth Classic [E1].
2. **Current Stock Availability**: The ESP32-WROOM-32D remains widely manufactured, fully supported in the ESP-IDF / Arduino Core toolchains, and ubiquitously available across African component supply chains [E6].
3. **Successor Path**: The pin-compatible `ESP32-WROOM-32E` represents the immediate active-lifecycle successor (incorporating minor ECO silicon bug fixes and identical dual-mode radio capabilities). Full evaluation of the WROOM-32E and ESP32-S3 successor paths is documented in `Final Verification and Risk Assessment Report.md` [E1/E2].

---

## 4. Governing Engineering Principles

Every datasheet and circuit implementation in this library adheres to five strict engineering invariants:

### Principle 1: The "No Silent Assumptions" Invariant
> *No hardware specification, electrical threshold, pin capability, current limit, connector type, library API, or compatibility claim may be inferred solely from the component's common name or from typical clone-module behavior. Where the exact component/module revision cannot be established from the source files, mark the value as an assumption, assign a risk level, and provide a verification procedure. Never silently substitute an assumed specification for a verified one.*

### Principle 2: 7-Level Source Evidence Hierarchy
All technical statements carry explicit evidence tags:
- `[E1]` Manufacturer Datasheet Verified (Official silicon datasheet)
- `[E2]` Official Board Documentation Verified (Espressif DevKitC V4 documentation)
- `[E3]` Module Documentation Verified (Breakout module documentation)
- `[E4]` Software / Library Documentation Verified (ESP32 Arduino Core 3.x)
- `[E5]` Engineering Calculation (First-principles mathematical/circuit analysis)
- `[E6]` Observed / Common Module Behavior (Empirical hobbyist observation; requires qualification)
- `[E7]` Assumption Requiring Physical Verification (Unverified parameter; requires test protocol)

### Principle 3: Pedagogical Drive & Power Rail Governance
> **GPIOs shall not directly drive motors, relay coils, high-current LEDs, or other loads requiring significant current. External transistor, MOSFET, or driver circuitry shall be used.** [E5]
- The ESP32 3V3 rail supplies the microcontroller and low-power sensor logic only [E2].
- Motors (DC 130), servos (SG90), stepper motors (28BYJ-48), and relay coils must draw power directly from the external 5V supply rail [E3/E5].

### Principle 4: Zero-Strapping-Pin Master Integration
In the simultaneous master capstone configuration, all boot strapping pins (`GPIO 0, 2, 5, 12, 15`) and internal SPI flash pins (`GPIO 6–11`) are strictly isolated and excluded to eliminate boot failures, flashing conflicts, or unexpected resets [E1/E2].

### Principle 5: ADC1-Only Concurrency
All analog sensors in the kit are mapped exclusively to ADC1 channels (`GPIO 32, 33, 34, 35, 36, 39`) so that analog measurement capability remains fully functional when Wi-Fi and Bluetooth radios are transmitting [E1/E4].

---

## 5. Library Organization & File Inventory

The library comprises exactly **50 Markdown files**:
- **6 Master Architecture & Integration Guides**:
  1. `README.md` (This document)
  2. `ESP32 vs Arduino UNO R3 Compatibility Matrix.md`
  3. `ESP32 Pinout and Pin Assignment Guide.md`
  4. `Hardware Modifications and Level Shifting Guide.md`
  5. `Software and Library Migration Guide.md`
  6. `Final Verification and Risk Assessment Report.md`
- **1 Microcontroller Pinout Datasheet**: `ESP32 DevKit Pinout Guide Datasheet.md` (replaces legacy Arduino UNO R3 guide)
- **1 Interface Cable Datasheet**: `USB Cable Datasheet.md` (updated for USB Type-A to Micro-USB)
- **42 Component Datasheets**: Dedicated datasheets for every sensor, actuator, display, and passive module in the kit.

---

## 6. Quickstart: Setting Up the ESP32 in Arduino IDE

1. **Install Arduino IDE**: Version 2.2.0 or higher.
2. **Add ESP32 Board URL**:
   In `File -> Preferences -> Additional Board Manager URLs`, enter:
   `https://espressif.github.io/arduino-esp32/package_esp32_index.json`
3. **Install Board Package**:
   In `Boards Manager`, search for `esp32` by Espressif Systems and install version **3.0.x** (Canonical target) [E4].
4. **Select Board**:
   Choose `ESP32 Dev Module` or `ESP32-WROOM-DA Module` (or `ESP32-DevKitC-V4` if listed).
5. **Port & Upload Settings**:
   - Upload Speed: `921600` (or `115200` for noisy cables)
   - CPU Frequency: `240MHz (WiFi/BT)`
   - Flash Frequency: `80MHz`
   - Flash Mode: `QIO`
   - Partition Scheme: `Default 4MB with spiffs (1.2MB APP/1.5MB SPIFFS)`
   - Port: Select the Silicon Labs CP210x COM port.
