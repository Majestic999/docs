# Rocket Switch (Rocker Switch) — Technical Datasheet

## General Description

A panel-mount single-pole single-throw (SPST) mechanical rocker switch featuring two fast-on spade terminals and an internal snap-action bistable spring contact mechanism. Used in the STEMAIDE Coder Pro Kit as an inline master DC power disconnect switch or as a manual digital mode selector [E1].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Switch Configuration** | SPST (Single Pole Single Throw, ON-OFF) | [E1] |
| **Contact Ratings** | 3A 250VAC / 6A 125VAC / 10A 12VDC | [E1] |
| **Contact Resistance** | $\le 30\text{ m}\Omega$ | [E1] |
| **Insulation Resistance**| $\ge 100\text{ M}\Omega$ at 500 V DC | [E1] |
| **Dielectric Strength** | 1500 V AC for 1 minute | [E1] |
| **Mechanical Life** | $\ge 20,000$ operations | [E1] |
| **Terminal Type** | 4.8 mm (3/16") Quick-Connect spade terminals / solder lugs | [E1] |
| **Mounting Hole Size** | ~19 × 13 mm snap-in rectangular cut-out | [E1] |

## Typical Wiring Applications

### Application 1: Inline Master DC Power Disconnect (Recommended)
Splice the rocker switch into the positive supply line between the external 5V power supply / battery pack and the ESP32 `5V / VIN` pin. This provides an instant physical emergency stop and bench power switch [E5].

### Application 2: Digital Mode Selector
Connect Terminal 1 to **`GPIO 13`** and Terminal 2 to **`GND`**, configuring the ESP32 pin as `pinMode(13, INPUT_PULLUP)` [E5].

## Source References
- Canal Electronic Rocker Switch Series: https://www.canal.com.tw
