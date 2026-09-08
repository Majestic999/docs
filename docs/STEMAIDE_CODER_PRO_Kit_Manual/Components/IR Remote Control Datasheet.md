# Infrared Remote Control — Technical Datasheet

## General Description

A 21-key slim handheld wireless optical remote control transmitter operating on a 38 kHz infrared carrier frequency using the standard NEC transmission protocol. It is powered by a 3V CR2025 lithium coin cell and serves as the optical transmitter companion to the kit's **VS1838B IR receiver module** [E1].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Transmission Protocol** | NEC Pulse Distance Coding Protocol | [E1] |
| **Carrier Frequency** | 38.0 kHz ± 1 kHz | [E1] |
| **Peak Wavelength** | 940 nm | [E1] |
| **Key Count** | 21 tactile dome keys (Power, Menu, Numbers 0–9, etc.) | [E1] |
| **Battery Type** | 3.0 V CR2025 Lithium Coin Cell | [E1] |
| **Operating Current** | < 15 mA (pulse transmission); < 1 µA (quiescent sleep) | [E1] |
| **Effective Range** | Up to 8 metres (line of sight) | [E1] |
| **Key Life** | $\ge 100,000$ actuations | [E1] |

## Button Mapping & NEC Command Codes

| Button Label | Function | NEC 8-Bit Command Code | Raw 32-Bit Hex Code |
|---|---|---|---|
| **POWER** | System Toggle | `0x45` | `0xFFA25D` |
| **MENU** | Mode Select | `0x46` | `0xFF629D` |
| **TEST** | Self-Test | `0x47` | `0xFFE21D` |
| **+** | Increase / Forward | `0x15` | `0xFFA857` |
| **−** | Decrease / Reverse | `0x07` | `0xFFE01F` |
| **BACK** | Return | `0x44` | `0xFF22DD` |
| **PLAY / PAUSE** | Action / Stop | `0x40` | `0xFF02FD` |
| **0 to 9** | Numeric Inputs | `0x16, 0x0C, 0x18, ...` | `0xFF6897, 0xFF30CF, ...` |

## NEC Protocol Timing Characteristics
- **Leading Pulse**: 9.0 ms burst followed by 4.5 ms space [E1].
- **Bit '0'**: 562.5 µs burst + 562.5 µs space (Total: 1.125 ms) [E1].
- **Bit '1'**: 562.5 µs burst + 1.6875 ms space (Total: 2.25 ms) [E1].
- **Repeat Code**: 9.0 ms burst followed by 2.25 ms space [E1].

## Source References
- SB-Projects NEC Infrared Transmission Protocol Reference: https://www.sbprojects.net/knowledge/ir/nec.php
