# USB 2.0 Cable Type A to Micro-B (100cm) — Technical Datasheet

## Product Reference

| Attribute | Value | Evidence Tier |
|---|---|---|
| **Product Type** | USB 2.0 Cable Type-A to Micro-USB (USB 2.0 Micro-B) | [E2] |
| **Cable Length** | 100 cm (1.0 metre) nominal | [E2] |
| **USB Standard** | USB 2.0 High-Speed (backwards compatible with USB 1.1) | [E1] |
| **Host Connector** | USB Type-A Male (Computer / Power Bank / USB Hub) | [E2] |
| **Device Connector** | USB Micro-B Male (matches official Espressif ESP32-DevKitC V4) | [E2] |
| **Data Transfer Rate** | Up to 480 Mbps | [E1] |
| **Cable Construction** | 28 AWG twisted data pair, 24 AWG power conductors, PVC jacket | [E5] |
| **Compatibility** | Espressif ESP32-DevKitC V4, ESP8266 NodeMCU, Android peripherals | [E2] |

> [!NOTE]
> **Board Hardware Receptacle Notice**:
> The official Espressif ESP32-DevKitC V4 reference board specifies a USB Micro-B receptacle [E2]. If using third-party variant boards featuring a USB Type-C connector, a standard USB Type-A to Type-C data cable must be substituted [E7].

## Pinout (USB Micro-B Device End)

| Pin # | Signal Name | Standard Wire Colour | Function & Description | Evidence |
|---|---|---|---|---|
| 1 | **VBUS (+5V)** | Red | +5.0 V DC power delivery from host computer | [E1] |
| 2 | **D−** | White | USB Differential Data Negative | [E1] |
| 3 | **D+** | Green | USB Differential Data Positive | [E1] |
| 4 | **ID** | N/C or Black | Pin 4 left unconnected in standard client USB-B mode | [E1] |
| 5 | **GND** | Black | Electrical power ground reference | [E1] |

## Pinout (USB Type-A Host End)

| Pin # | Signal Name | Standard Wire Colour | Function & Description | Evidence |
|---|---|---|---|---|
| 1 | **VBUS (+5V)** | Red | +5.0 V DC power source | [E1] |
| 2 | **D−** | White | USB Differential Data Negative | [E1] |
| 3 | **D+** | Green | USB Differential Data Positive | [E1] |
| 4 | **GND** | Black | Ground reference | [E1] |

## Electrical Characteristics

| Parameter | Value | Test Condition / Notes | Evidence |
|---|---|---|---|
| **Operating Voltage** | 5.0 V DC nominal (4.75 V to 5.25 V) | Host USB port standard | [E1] |
| **Maximum Current** | Up to 1.8 A (continuous power rating) | 24 AWG power conductors | [E5] |
| **Differential Impedance**| 90 Ω ±15% | High-Speed USB signal integrity | [E1] |
| **Contact Plating** | Gold-plated phosphor bronze | Low contact resistance (< 30 mΩ) | [E2] |
| **Shielding** | 100% Aluminium Mylar foil + braided copper braid | EMI/RFI suppression | [E5] |

## Function in the STEMAIDE Coder Pro Kit

1. **Firmware Upload & Flashing**: Provides the high-speed serial data link between the host PC running Arduino IDE and the onboard Silicon Labs CP2102N USB-to-UART bridge on the ESP32-DevKitC V4 board [E2].
2. **Serial Telemetry & Debugging**: Carries bidirectional UART serial communication at speeds up to 921,600 baud for the Arduino IDE Serial Monitor and Serial Plotter [E4].
3. **5V Power Delivery**: Supplies clean 5.0V power directly to the ESP32 board's onboard LDO regulator and peripheral sensors during lab programming and experiments [E2].

## Source References
- Universal Serial Bus Specification Revision 2.0: https://www.usb.org/document-library/usb-20-specification
- Espressif ESP32-DevKitC V4 Hardware Reference: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/esp32/get-started-devkitc.html
