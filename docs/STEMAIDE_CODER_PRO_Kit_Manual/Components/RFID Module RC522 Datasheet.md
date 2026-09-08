# RFID Module RC522 (13.56 MHz) — Technical Datasheet

## General Description

The RC522 is a highly integrated contactless reader/writer communication module operating at 13.56 MHz based on the NXP MFRC522 transceiver IC. It utilizes advanced modulation and demodulation concepts for all types of passive 13.56 MHz contactless communication methods and protocols according to the ISO/IEC 14443 Type A standard [E1].

### Native 3.3V Parity with the ESP32 Platform
The MFRC522 IC operates strictly at **2.5V to 3.3V DC** ($V_{DD\max} = 3.6\text{V}$) [E1]. On the legacy 5V Arduino UNO R3, connecting 5V SPI signals (MOSI, SCK, SS) directly to the RC522 severely stressed the silicon inputs [E1].

When interfacing with the **Espressif ESP32-DevKitC V4**:
1. **100% Native Voltage Parity**: The ESP32 operates at 3.3V LVTTL, matching the RC522 perfectly on all SPI signals without requiring level shifters [E1/E5].
2. **High-Speed Hardware VSPI**: The module connects directly to the ESP32's hardware VSPI controller (**`GPIO 18 SCK`**, **`GPIO 19 MISO`**, **`GPIO 23 MOSI`**, **`GPIO 4 SS`**, **`GPIO 14 RST`**) operating at clock frequencies up to 10 MHz [E1/E4].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Transceiver IC** | NXP MFRC522 Contactless Reader IC | [E1] |
| **Operating Frequency** | 13.56 MHz (HF ISM Band) | [E1] |
| **Operating Voltage ($V_{DD}$)** | **2.5 V to 3.3 V DC (Connect strictly to ESP32 3V3 Rail)** | [E1/E5] |
| **Current Consumption** | 13 mA to 26 mA (Operating); 10 µA (Hard power-down) | [E1] |
| **Host Interface** | SPI (Serial Peripheral Interface), up to 10 Mbit/s | [E1] |
| **Supported Card Types** | MIFARE Classic 1K/4K, MIFARE Ultralight, NTAG213/215/216 | [E1] |
| **Operating Distance** | Up to 50 mm (5.0 cm) depending on antenna geometry | [E1] |
| **Antenna Type** | Onboard differential printed PCB loop antenna | [E1] |
| **Logic Compatibility** | **3.3 V LVTTL Direct Connection** | [E5] |

## Pinout & ESP32 VSPI Wiring

| Pin Label | Function | ESP32-DevKitC V4 Connection | Description |
|---|---|---|---|
| **SDA / SS** | SPI Slave Select | **GPIO 4** (Dedicated Chip Select) | Active-LOW Chip Select (replaces strapping pin GPIO 5) [E5] |
| **SCK** | SPI Clock | **GPIO 18 (VSPI SCK)** | Hardware SPI Serial Clock [E4] |
| **MOSI** | Master Out Slave In | **GPIO 23 (VSPI MOSI)** | Hardware SPI Master Out Slave In [E4] |
| **MISO** | Master In Slave Out | **GPIO 19 (VSPI MISO)** | Hardware SPI Master In Slave Out [E4] |
| **IRQ** | Interrupt Request | *Unconnected* (Optional GPIO) | Active-LOW interrupt output [E1] |
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **RST** | Reset / Power Down | **GPIO 14** | Active-LOW hardware reset [E5] |
| **3.3V** | Power Supply | **3V3 Rail** | 3.3V DC regulated supply (Do NOT connect to 5V!) [E1] |

```
   ESP32-DevKitC V4                      RC522 RFID Module
  ┌────────────────┐                     ┌─────────────────┐
  │            3V3 ├────────────────────►│ 3.3V (VCC)      │
  │        GPIO 14 ├────────────────────►│ RST             │
  │         GPIO 4 ├────────────────────►│ SDA / SS        │
  │        GPIO 18 ├────────────────────►│ SCK             │
  │        GPIO 23 ├────────────────────►│ MOSI            │
  │        GPIO 19 ├◄────────────────────┤ MISO            │
  │            GND ├─────────────────────┤ GND             │
  └────────────────┘                     └─────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Reading Card UID

```cpp
#include <SPI.h>
#include <MFRC522.h>

const int SS_PIN = 4;   // Chip Select on GPIO 4
const int RST_PIN = 14; // Reset on GPIO 14

MFRC522 rfid(SS_PIN, RST_PIN);

void setup() {
  Serial.begin(115200);

  // Initialize ESP32 VSPI bus explicitly
  SPI.begin(18, 19, 23, SS_PIN); // SCK=18, MISO=19, MOSI=23, SS=4
  rfid.PCD_Init();

  Serial.println("ESP32 RC522 RFID Reader Ready. Tap a card...");
  rfid.PCD_DumpVersionToSerial();
}

void loop() {
  // Check for new RFID cards
  if (!rfid.PICC_IsNewCardPresent() || !rfid.PICC_ReadCardSerial()) {
    return;
  }

  Serial.print(">>> Card Detected! UID: ");
  for (byte i = 0; i < rfid.uid.size; i++) {
    Serial.printf("%02X ", rfid.uid.uidByte[i]);
  }
  Serial.printf("| Card Type: %s\n", rfid.PICC_GetTypeName(rfid.PICC_GetType(rfid.uid.sak)));

  // Halt card and stop encryption
  rfid.PICC_HaltA();
  rfid.PCD_StopCrypto1();
  delay(1000);
}
```

## Source References
- NXP MFRC522 Standard 3.3V Contactless Reader IC Datasheet: https://www.nxp.com/docs/en/data-sheet/MFRC522.pdf
- Github Community MFRC522 Arduino Library: https://github.com/OSSLibraries/Arduino_MFRC522v2
