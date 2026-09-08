# ESP32 DevKit Pinout Guide — Technical Datasheet

## General Description

The **ESP32-DevKitC V4** is a 32-bit dual-core development board manufactured by Espressif Systems based on the **ESP32-WROOM-32D** module [E2]. It features a high-performance Tensilica Xtensa 32-bit LX6 microprocessor operating up to 240 MHz, integrated 2.4 GHz Wi-Fi (802.11 b/g/n), dual-mode Bluetooth (Classic BR/EDR and BLE), 520 KB SRAM, 4 MB external Quad-SPI flash, and 38 dual-row header pins [E1/E2].

The ESP32-DevKitC V4 serves as the primary microcontroller and IoT edge platform for the **STEMAIDE Coder Pro Kit — ESP32 Edition**, replacing the legacy 8-bit Arduino UNO R3 while providing vastly expanded processing speed, memory, wireless networking, and multi-peripheral connectivity [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Microcontroller / Module** | Espressif ESP32-WROOM-32D (ESP32-D0WD-V3 silicon, rev 3.0) | [E1] |
| **CPU Architecture** | Dual-core Tensilica Xtensa 32-bit LX6 | [E1] |
| **Clock Frequency** | Configurable from 80 MHz to 240 MHz (default: 240 MHz) | [E1] |
| **Flash Memory** | 4 MB (32 Mbit) external Quad-SPI Flash (tied to GPIO 6–11) | [E1] |
| **SRAM** | 520 KB internal SRAM (320 KB DRAM, 200 KB IRAM) | [E1] |
| **ROM** | 448 KB internal ROM for bootloader and core functions | [E1] |
| **Operating Voltage (Internal)** | 3.3 V DC | [E1] |
| **Input Supply Voltage (External)** | 5.0 V DC nominal (via Micro-USB or 5V/VIN pin; operating limits: 4.5V–6.0V) | [E2] |
| **I/O Logic Level** | **3.3 V LVTTL (Strictly NOT 5V tolerant)** | [E1] |
| **Absolute Maximum Pin Voltage** | $V_{DD} + 0.3\text{ V} \approx 3.6\text{ V}$ | [E1] |
| **DC Current per Output Pin** | Recommended continuous: $\le 12\text{ mA}$ (nominal drive strength level 2); Max limit: 20 mA | [E1/E5] |
| **Pedagogical Drive Rule** | **GPIOs shall not directly drive motors, coils, or high currents. External drivers required.** | [E5] |
| **ADC Channels** | 18 channels, 12-bit resolution (0–4095); ADC1: 8 channels; ADC2: 10 channels | [E1] |
| **DAC Channels** | 2 channels, 8-bit true analog output (GPIO 25, GPIO 26) | [E1] |
| **PWM (LEDC) Channels** | 16 independent hardware channels, 1–16 bit resolution, frequency up to 40 MHz | [E1/E4] |
| **Hardware UARTs** | 3 independent controllers (UART0: USB monitor; UART1: unassigned; UART2: default GPIO 16/17) | [E1] |
| **Hardware SPI Buses** | 2 user buses: VSPI (default GPIO 18, 19, 23, 5/4) and HSPI (GPIO 12, 13, 14, 15) | [E1] |
| **Hardware I²C Controllers** | 2 independent controllers (default Wire: SDA on GPIO 21, SCL on GPIO 22) | [E1] |
| **Capacitive Touch Sensors** | 10 capacitive touch inputs (T0–T9) | [E1] |
| **Integrated Wireless** | Wi-Fi 802.11 b/g/n (up to 150 Mbps) + Bluetooth v4.2 BR/EDR & BLE | [E1] |
| **Form Factor / Header Pitch** | 38-pin dual-row headers, 0.1" (2.54 mm) pin pitch, width 25.4–27.9 mm | [E2] |
| **Operating Temperature** | –40 °C to +85 °C | [E1] |

## Power Architecture

| Pin Label | Voltage | Direction | Function & Usage Notes | Evidence |
|---|---|---|---|---|
| **5V / VIN** | 5.0 V (4.5–6.0V) | Input / Output | Connects to USB 5V rail via Schottky diode or receives external 5V regulated input [E2]. | [E2] |
| **3V3** | 3.3 V DC | Output | Output of onboard 3.3V LDO regulator (SGM2211 / AMS1117) [E2]. Powers 3.3V sensors only [E5]. | [E2/E5] |
| **GND** | 0 V | Common | Common ground reference (3 pins available on headers; all internally connected) [E2]. | [E2] |
| **EN** | 3.3 V pull-up | Input | Chip Enable / Reset (Active LOW). Pulled LOW by onboard EN button to reset MCU [E1]. | [E1/E2] |

> [!WARNING]
> **Power Rail Governance Rule**:
> The 3V3 pin must not be used as a power supply for motors (DC 130), servos (SG90), stepper motors (28BYJ-48), or relay coils! These inductive and high-current loads must be powered directly from the external 5V supply rail with common ground connected to the ESP32 GND pin [E5].

## Pinout Tables

### Power & Control Pins

| Header Pin # | Label | Primary Function | Electrical Characteristics |
|---|---|---|---|
| 1 | 3V3 | 3.3V Regulated Output | Powers external 3.3V sensors (Max total peripheral load $\le 250\text{ mA}$) [E5] |
| 2 | EN | Reset (Active Low) | Pulled to 3.3V via $10\text{ k}\Omega$ internal resistor; ground to reset [E1/E2] |
| 14 | GND | Ground Reference | Common ground [E2] |
| 19 | 5V | 5V DC Supply Input/Output | Connected to Micro-USB VBUS via diode [E2] |
| 32 | GND | Ground Reference | Common ground [E2] |
| 38 | GND | Ground Reference | Common ground [E2] |

### Analog-to-Digital Converter (ADC1) — Wi-Fi Concurrent Pins

All analog sensors in the STEMAIDE Coder Pro Kit must connect to **ADC1** pins to maintain active analog measurement capability while Wi-Fi and Bluetooth radios are transmitting [E1/E5]:

| Header Pin # | GPIO Label | ADC Channel | Input Type | Master Kit Assignment |
|---|---|---|---|---|
| 3 | GPIO 36 | ADC1_CH0 / SENSOR_VP | Input Only (No Pull-up) | XY Joystick X-Axis (VRx) [E5] |
| 4 | GPIO 39 | ADC1_CH3 / SENSOR_VN | Input Only (No Pull-up) | XY Joystick Y-Axis (VRy) [E5] |
| 5 | GPIO 34 | ADC1_CH6 / VDET_1 | Input Only (No Pull-up) | Potentiometer 10 kΩ [E5] |
| 6 | GPIO 35 | ADC1_CH7 / VDET_2 | Input Only (No Pull-up) | Photoresistor LDR Divider [E5] |
| 7 | GPIO 32 | ADC1_CH4 / TOUCH9 | Bidirectional / ADC1 | Water Test Module / Stepper IN3 [E5] |
| 8 | GPIO 33 | ADC1_CH5 / TOUCH8 | Bidirectional / ADC1 | Sound Sensor AO / LM35 / Stepper IN4 [E5] |

### Safe General-Purpose Digital & Communication Pins

| Header Pin # | GPIO Label | Primary Functions | Master Kit Assignment |
|---|---|---|---|
| 9 | GPIO 25 | General I/O, DAC1, LEDC PWM | Stepper IN1 / Relay Driver [E5] |
| 10 | GPIO 26 | General I/O, DAC2, LEDC PWM | Stepper IN2 / Active Buzzer [E5] |
| 11 | GPIO 27 | General I/O, LEDC PWM, Touch 7 | MAX7219 CS / SG90 Servo PWM [E5] |
| 12 | GPIO 14 | General I/O, HSPI CLK, Touch 6 | HC-SR04 Echo (via 1k/2k Divider) [E5] |
| 15 | GPIO 13 | General I/O, HSPI MOSI, Touch 4 | HC-SR04 Trigger / Pushbutton [E5] |
| 26 | GPIO 4 | General I/O, HSPI HD, Touch 0 | RC522 Chip Select (SS) / DS18B20 [E5] |
| 27 | GPIO 16 | Hardware UART2 RX (RX2) | HC-05 Bluetooth Module TXD [E5] |
| 28 | GPIO 17 | Hardware UART2 TX (TX2) | HC-05 Bluetooth Module RXD [E5] |
| 30 | GPIO 18 | Hardware VSPI SCK | Shared SPI Clock (RC522 / MAX7219) [E4] |
| 31 | GPIO 19 | Hardware VSPI MISO | Shared SPI Data In (RC522 MISO) [E4] |
| 33 | GPIO 21 | Hardware I²C SDA (Wire) | Shared I²C Data (LCD 1602 / BME280 / RTC) [E4] |
| 34 | GPIO 3 | Hardware UART0 RX (RX0) | Hardwired to USB Serial Bridge [E2] |
| 35 | GPIO 1 | Hardware UART0 TX (TX0) | Hardwired to USB Serial Bridge [E2] |
| 36 | GPIO 22 | Hardware I²C SCL (Wire) | Shared I²C Clock (LCD 1602 / BME280 / RTC) [E4] |
| 37 | GPIO 23 | Hardware VSPI MOSI | Shared SPI Data Out (RC522 / MAX7219) [E4] |

### Boot Strapping Pins (Avoided in Master Integration)

| Header Pin # | GPIO Label | Default Boot State Requirement | Reason for Exclusion in Master Configuration |
|---|---|---|---|
| 25 | GPIO 0 | Must be HIGH for SPI flash boot | Attached to BOOT pushbutton; pulling LOW at reset enters UART download mode [E1]. |
| 24 | GPIO 2 | Must be LOW or floating during flashing | Attached to onboard blue LED; connected loads can prevent program flashing [E1]. |
| 29 | GPIO 5 | Emits high-frequency PWM during boot | SDIO timing strapping pin; must not be pulled LOW during power-up reset [E1]. |
| 13 | GPIO 12 | **Must be LOW during power-up** | Flash voltage selector. Pulling HIGH selects 1.8V flash rail, crashing 3.3V flash [E1]! |
| 23 | GPIO 15 | Controls boot debug logging | Pulling LOW disables silent boot; leave isolated from capacitive loads [E1]. |

### Forbidden Pins: Integrated SPI Flash (DO NOT CONNECT)

| Header Pin # | GPIO Label | Dedicated Internal Function | Danger Level |
|---|---|---|---|
| 20 | GPIO 6 | Flash SPI Clock (CLK) | **FORBIDDEN** — Connecting causes instant MCU crash [E1] |
| 21 | GPIO 7 | Flash SPI Data 0 (SD0) | **FORBIDDEN** — Connecting causes instant MCU crash [E1] |
| 22 | GPIO 8 | Flash SPI Data 1 (SD1) | **FORBIDDEN** — Connecting causes instant MCU crash [E1] |
| 16 | GPIO 9 | Flash SPI Data 2 (SD2) | **FORBIDDEN** — Connecting causes instant MCU crash [E1] |
| 17 | GPIO 10 | Flash SPI Data 3 (SD3) | **FORBIDDEN** — Connecting causes instant MCU crash [E1] |
| 18 | GPIO 11 | Flash SPI Command (CMD) | **FORBIDDEN** — Connecting causes instant MCU crash [E1] |

## Breadboard Layout & Physical Insertion

The ESP32-DevKitC V4 is designed to straddle the central dividing trough of a standard 830-point breadboard [E5]:

```
   (Row A) Accessible Tie-Points for Jumper Wires ──► [ ]  [●] ◄── Left Pin Header (Pin 1 to 19)
   Breadboard Center Ravine ───────────────────────────── ║
   Right Pin Header (Pin 20 to 38) ──────────────────► [●]  [ ] ◄── Accessible Tie-Points (Row J)
```

## Source References
- Espressif Systems ESP32-WROOM-32D Datasheet: https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32d_esp32-wroom-32u_datasheet_en.pdf
- Espressif ESP32-DevKitC V4 User Guide: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/esp32/get-started-devkitc.html
- ESP32 Hardware Design Guidelines: https://www.espressif.com/sites/default/files/documentation/esp32_hardware_design_guidelines_en.pdf
