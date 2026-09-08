# Resistor Pack (Assorted Values) — Technical Datasheet

## General Description

A comprehensive assortment of through-hole axial leaded carbon-film or metal-film fixed resistors rated at 1/4W (0.25W) with 1% or 5% tolerance. Used throughout the STEMAIDE Coder Pro Kit for LED current-limiting, pull-up/pull-down bias networks, sensor dividers, and the **mandatory 1 kΩ / 2 kΩ level-shifting divider for the HC-SR04 ultrasonic echo pin** [E1/E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Power Rating** | 0.25 W (1/4 Watt) at 70 °C ambient | [E1] |
| **Resistance Tolerance** | ±1% (Metal Film, 5-band) or ±5% (Carbon Film, 4-band) | [E1] |
| **Max Working Voltage** | 250 V DC / RMS | [E1] |
| **Dielectric Withstand**| 500 V | [E1] |
| **Operating Temperature** | −55 °C to +155 °C | [E1] |
| **Lead Pitch** | Formed for standard 0.3" / 0.4" breadboard hole spacing | [E1] |

## Typical Resistance Values & Roles in the ESP32 Kit

| Nominal Value | 4-Band Colour Code | Critical Role in STEMAIDE ESP32 Kit | Evidence |
|---|---|---|---|
| **100 Ω** | Brown, Black, Brown, Gold | Blue and White 5mm LED current limiting at 3.3V | [E5] |
| **150 Ω** | Brown, Green, Brown, Gold | Green 5mm LED and RGB LED module current limiting at 3.3V | [E5] |
| **220 Ω** | Red, Red, Brown, Gold | Red and Yellow LED limiting; 7-Segment display segment resistors | [E5] |
| **1.0 kΩ** | Brown, Black, Red, Gold | Series resistor $R_1$ in HC-SR04 Echo voltage divider; NPN base drive | [E5] |
| **2.0 kΩ** | Red, Black, Red, Gold | Shunt resistor $R_2$ in HC-SR04 Echo voltage divider (or two 1.0 kΩ in series) | [E5] |
| **4.7 kΩ** | Yellow, Violet, Red, Gold | 1-Wire pull-up resistor for DS18B20 digital thermometer | [E1/E5] |
| **10.0 kΩ**| Brown, Black, Orange, Gold| Fixed reference resistor for Photoresistor (LDR) voltage divider | [E1/E5] |

## Source References
- Yageo Resistor Specifications: https://www.yageo.com
- Vishay Dale Fixed Resistor Design Reference: https://www.vishay.com
