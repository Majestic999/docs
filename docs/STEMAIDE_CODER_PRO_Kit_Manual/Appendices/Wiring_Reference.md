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
