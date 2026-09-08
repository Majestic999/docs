# RFID Keychain and Card (13.56 MHz) — Technical Datasheet

## General Description

Passive contactless radio-frequency identification (RFID) transponders operating at the 13.56 MHz High Frequency (HF) ISM band. The kit includes one ISO/IEC 14443 Type A credit-card format PVC smart card and one blue ABS key fob tag. They operate as the contactless identification targets for the kit's **RC522 RFID reader/writer module** [E1].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Operating Frequency** | 13.56 MHz (HF ISM band) | [E1] |
| **Communication Protocol** | ISO/IEC 14443 Type A standard | [E1] |
| **IC Type** | MIFARE Classic 1K (NXP S50 compatible) | [E1] |
| **Memory Size** | 1024 bytes (1 KB EEPROM arranged in 16 sectors of 4 blocks) | [E1] |
| **Read / Write Distance** | 2 cm to 5 cm (with RC522 PCB antenna) | [E1] |
| **Data Retention** | $\ge 10$ years | [E1] |
| **Write Endurance** | $\ge 100,000$ write/erase cycles | [E1] |
| **Card Dimensions** | 85.6 × 54.0 × 0.8 mm (ISO standard credit card format) | [E1] |
| **Key Fob Dimensions** | 40 × 32 × 4 mm (Blue ultrasonic-welded ABS shell) | [E1] |

## Memory Map (MIFARE Classic 1K)
- **Sector 0, Block 0 (Manufacturer Block)**: Contains 4-byte or 7-byte unique serial number (UID) and manufacturer data (Read-only, locked at factory) [E1].
- **Sectors 0 to 15, Blocks 0 to 2**: General data storage (Read/Write accessible via security keys) [E1].
- **Sector Trailer (Block 3 in each sector)**: Stores Key A (6 bytes), Access Bits (4 bytes), and optional Key B (6 bytes) [E1].

## Source References
- NXP MIFARE Classic 1K Contactless Security IC Datasheet: https://www.nxp.com
