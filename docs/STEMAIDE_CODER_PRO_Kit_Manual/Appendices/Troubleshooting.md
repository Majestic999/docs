# Appendix: Troubleshooting and Diagnostics

This appendix provides a structured diagnostic guide to help you find and fix problems when your circuits do not work as expected.

---

## 1. Quick Check Checklist (The "Rule of Three")

Before writing new code or rebuilding circuits, always verify these three essential elements:

1.  **Power Check**: Is the green "ON" LED glowing steadily on the Arduino board? If it is off or dim, immediately unplug the board (you have a short circuit).
2.  **Port Check**: Does `Tools -> Port` show your board as selected? If not, reconnect the USB cable or try a different port/cable.
3.  **Ground Check**: Do all negative pins of your breadboard components connect to the Arduino **GND** pin? An open ground loop is the most common reason circuits fail to respond.

---

## 2. Diagnosing Inputs & Sensors

If you are not receiving sensor readings (such as distance or light readings), use this step-by-step diagnostic process.

### Step 1: Use the Serial Monitor
Always output raw sensor readings to the Serial Monitor to verify data. Add this code to check your input:
```cpp
void setup() {
  Serial.begin(9600);
}
void loop() {
  int rawValue = analogRead(A0);
  Serial.print("Raw Sensor Value: ");
  Serial.println(rawValue);
  delay(200);
}
```
Open the Serial Monitor (`Ctrl+Shift+M`) at 9600 baud. Observe the readings:
*   **Reading stays stuck at 0 or 1023**: This indicates a wiring issue. The input pin is either disconnected (floating) or shorted directly to GND or 5V. Check breadboard row alignment.
*   **Value does not change with external stimuli**: (e.g., LDR values stay the same when you cover it). The sensor is not wired correctly in series/voltage divider configuration. Check resistors.

### Step 2: Calibrating Sensor Thresholds
Ambient conditions (such as room light or acoustics) affect sensors like LDRs and sound modules. 
1.  Read the raw sensor values in the dark and under direct light.
2.  Find the midpoint between these values. For example, if "dark" reads 200 and "bright" reads 800, the midpoint is `(200 + 800) / 2 = 500`.
3.  Use this midpoint as your threshold in your conditional statements (e.g., `if (lightVal < 500)`).

---

## 3. Diagnosing Output Devices (Actuators)

If you send signals to LEDs, buzzers, or motors but nothing happens, try these diagnostics.

### Test the Actuator Directly
*   **For LEDs and Buzzers**: Temporarily unplug the control wire from the Arduino digital pin and plug it directly into the **5V** power pin.
    *   *If it activates*: The hardware is fine; the issue is with your Arduino pin configuration, code logic, or the physical digital pin.
    *   *If it does not activate*: The component is wired backwards (reverse polarity), is in the wrong breadboard row, has a broken jumper wire, or is damaged.

### Servo Motor Problems
*   **Servo sweeps continuously or twitches**: Servo motors draw high spikes of electrical current when starting a rotation. If the Arduino power regulator cannot supply this, the board's voltage drops, resetting the Arduino loop.
    *   *Fix*: Add a decoupling capacitor (e.g., 100µF) across the 5V and GND lines of the servo, or use an external battery pack to power the servo directly, linking the battery ground to the Arduino GND.
*   **Servo does not move to the specified angle**: Make sure you have included `#include <Servo.h>` and that you call `.attach(pin)` in `void setup()`. Verify you are sending a value between 0 and 180.

---

## 4. Multimeter Troubleshooting (If Available)

If you have access to a digital multimeter, you can run these simple checks:
*   **Continuity Test**: Set the dial to the speaker/beep icon. Touch probes to both ends of a jumper wire to confirm the wire is not broken internally.
*   **Voltage Test**: Set the dial to DC Voltage (20V range). Touch the black probe to GND and the red probe to the Arduino 5V pin. The meter should display between 4.8V and 5.2V.
