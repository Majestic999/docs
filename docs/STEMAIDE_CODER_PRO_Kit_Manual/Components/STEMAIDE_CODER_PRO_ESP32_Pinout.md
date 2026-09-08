# [Board Pinout Mapping] ESP32 DevKitC V4: Complete Physical Header Pin (1-38) to GPIO Mapping

> **Normative Hardware Baseline**: Espressif ESP32-DevKitC V4 with ESP32-WROOM-32D (Tensilica Xtensa LX6 @ 240 MHz).
> **Operating Voltage**: 3.3V LVTTL (STRICTLY NOT 5V TOLERANT). 38-pin dual-row headers.

## Complete 38-Pin Physical to GPIO Mapping Tables

This table definitively links every physical header pin (1 to 38) on the ESP32-DevKitC V4 to its internal GPIO designation, ADC1 channel, and STEMAIDE Master Kit assignment:

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


## Critical Board Hardware Rules
- **Physical Header Pin 1 (3V3)**: Regulated 3.3V output. Connect potentiometers, LDRs, and analog sensors here ONLY.
- **Physical Header Pin 19 (5V / VIN)**: 5.0V power rail from USB. Powers servos (SG90), relays (JD-VCC), motors, and LCD power.
- **ADC1 Wi-Fi Safe Pins**: GPIO 36 (Pin 3), GPIO 39 (Pin 4), GPIO 34 (Pin 5), GPIO 35 (Pin 6), GPIO 32 (Pin 7), GPIO 33 (Pin 8).
- **Default I2C (Wire) Pins**: GPIO 21 (Header Pin 33 / SDA), GPIO 22 (Header Pin 36 / SCL).
- **HC-05 Hardware UART2**: GPIO 16 (Header Pin 27 / RX2), GPIO 17 (Header Pin 28 / TX2).
- **Forbidden Pins (DO NOT CONNECT)**: GPIO 6, 7, 8, 9, 10, 11 (Header Pins 16, 17, 18, 20, 21, 22) — Connected to internal Flash.
