# Appendix: Wiring Reference

This guide provides basic wiring and circuit building instructions to ensure safe and correct connections.

---

## 1. Breadboard Layout Rules

Breadboards are designed with internal metal clips that connect specific columns and rows.

*   **Horizontal Power Rails (Top and Bottom Rows)**: Labeled with a red `+` and blue `-` line. All holes in a rail are connected horizontally. Use them to distribute power (5V) and Ground (GND) across multiple modules.
*   **Vertical Rows (1 to 30 or 60)**: Columns A, B, C, D, and E are connected vertically. Columns F, G, H, I, and J are also connected vertically. A component inserted in A5 is connected to a wire inserted in B5, C5, D5, or E5.
*   **Center Channel**: Separates the left vertical rows from the right vertical rows. A component bridged across this channel has its opposite pins electrically isolated.

---

## 2. Standard Circuit Schematics

### LED Wiring (With Resistor)
Always connect a 220Ω current-limiting resistor in series with an LED to prevent damage.

```
[Arduino Pin] ------ [220Ω Resistor] ------ [LED Anode (+)]
                                               |
                                            [LED Cathode (-)] ------ [GND Pin]
```

### Push Button Wiring (Active-Low / Pull-Up)
Using the internal `INPUT_PULLUP` resistor simplifies button wiring. When the button is pressed, it connects the pin directly to GND (registers as `LOW`).

```
[Arduino Input Pin (Pin 2)] ------ [Push Button Pin A]
                                      |
                                      |  (Pressing button closes circuit)
                                      |
                                   [Push Button Pin B] ------ [GND Pin]
```

---

## 3. Wiring Checklist for Safe Prototyping

1.  **Always disconnect the USB cable before making changes**: Wiring components while power is active can cause accidental short circuits that damage the board.
2.  **Color code your wires**:
    *   **Red** = 5V Power
    *   **Black / Blue** = GND Ground
    *   **Yellow / Green / White** = Signals (Sensor inputs or actuator control)
3.  **Ensure connections are deep and secure**: Wires should sit firmly inside the breadboard holes. Loose wires cause erratic sensor noise and intermittent behavior.


---

## 3. ESP32 3.3V Voltage Protection & Level Shifting Rules

> [!IMPORTANT]
> The ESP32 operates at **3.3V logic** and is **NOT 5V tolerant**. Exceeding 3.6V on any GPIO pin can permanently damage the chip!

### Rule 1: Potentiometers & Analog Sensors on 3.3V
Always connect the VCC / outer power pin of potentiometers, LDRs, water level sensors, and soil moisture sensors to the **3.3V pin** on the ESP32 board. This guarantees analog voltage output never exceeds 3.3V.

### Rule 2: HC-SR04 Ultrasonic Echo Voltage Divider
When the HC-SR04 is powered by 5V, its Echo pin outputs a 5V signal. You MUST insert a 1kΩ / 2kΩ resistor divider:
```
[HC-SR04 ECHO (5V)] ────[ 1kΩ ]────┬────► [ESP32 GPIO (Safe 3.3V)]
                                   │
                                 [ 2kΩ ]
                                   │
                                 [ GND ]
```


---

## 3. ESP32 3.3V Voltage Protection & Level Shifting Rules

> [!IMPORTANT]
> The ESP32 operates at **3.3V logic** and is **NOT 5V tolerant**. Exceeding 3.6V on any GPIO pin can permanently damage the chip!

### Rule 1: Potentiometers & Analog Sensors on 3.3V
Always connect the VCC / outer power pin of potentiometers, LDRs, water level sensors, and soil moisture sensors to the **3.3V pin** on the ESP32 board. This guarantees analog voltage output never exceeds 3.3V.

### Rule 2: HC-SR04 Ultrasonic Echo Voltage Divider
When the HC-SR04 is powered by 5V, its Echo pin outputs a 5V signal. You MUST insert a 1kΩ / 2kΩ resistor divider:
```
[HC-SR04 ECHO (5V)] ────[ 1kΩ ]────┬────► [ESP32 GPIO (Safe 3.3V)]
                                   │
                                 [ 2kΩ ]
                                   │
                                 [ GND ]
```
