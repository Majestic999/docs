# 9V Battery Snap Connector — Technical Datasheet

## General Description

A heavy-duty PP3 9V battery clip connector with 15 cm 24 AWG colour-coded flying leads (red = positive, black = negative). It connects standard 9V alkaline or rechargeable batteries to provide portable power for embedded hardware [E1].

When powering the **Espressif ESP32-DevKitC V4**, the red lead connects to the **`5V` (or `VIN`) pin** and the black lead connects to **`GND`** [E2]. 

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Battery Type** | Standard 9V PP3 (6LR61 / 6F22) | [E1] |
| **Output Voltage** | 9.0 V DC nominal (fresh alkaline: ~9.5V; cut-off: 6.0V) | [E1] |
| **Typical Capacity** | 500–600 mAh (alkaline) | [E1] |
| **Lead Wire Gauge** | 24 AWG stranded copper | [E1] |
| **Lead Length** | ~150 mm (15 cm) | [E1] |
| **Connector Type** | T-type or I-type moulded vinyl snap | [E1] |
| **Target Board Input** | **ESP32-DevKitC V4 VIN / 5V Pin** | [E2] |

## Thermal Dissipation & Power Considerations

The ESP32-DevKitC V4 onboard linear regulator (AMS1117-3.3 or SGM2211) steps the 9.0V battery down to 3.3V [E2]. The voltage drop across the regulator is:

$$\Delta V = 9.0\text{ V} - 3.3\text{ V} = 5.7\text{ V} \quad [\text{E5}]$$

Thermal power dissipated as heat in the regulator chip:
- At idle ($I \approx 80\text{ mA}$): $P = 5.7\text{ V} \times 0.08\text{ A} = 0.456\text{ W}$ (Warm) [E5].
- During Wi-Fi transmission ($I \approx 200\text{ mA}$ average): $P = 5.7\text{ V} \times 0.20\text{ A} = 1.14\text{ W}$ (Significant heating!) [E5].

> [!WARNING]
> **Classroom Power Best Practice**:
> Due to high thermal dissipation and the limited 500 mAh capacity of 9V PP3 batteries, 9V batteries should be reserved for short portable demonstrations. For extended IoT labs, a 5V USB power bank or external 5V 2A DC adapter connected via USB is strongly recommended [E5].

## Source References
- Energizer 9V Alkaline Battery Technical Specifications: https://data.energizer.com
- Espressif ESP32-DevKitC V4 Schematic: https://dl.espressif.com/dl/schematics/esp32_devkitc_v4-sch.pdf
