---
hide:
  - toc
---
# Beginner Projects: LED Control

Welcome to the **Beginner Projects** section of the STEMAIDE ESP32 board manual! In this foundational project, you will learn how to mount components on a breadboard, establish proper electrical connections, and program the ESP32 to control a basic LED output.

## Components Used

To build this project, gather the following components from your kit:

- **ESP32 Development Board** — The main microcontroller board.

- **Breadboard** — A solderless board used to prototype circuits using rows and columns.

- **LED (Light Emitting Diode)** — Output device that produces light.

- **220Ω Resistor** — Current-limiting resistor to protect the LED from burning out.

- **Jumper Wires** — Male-to-male wires used to establish electrical connections.

---

## Mounting Instructions

Follow these step-by-step instructions to insert your components correctly into the breadboard:

1. **Mounting the ESP32**:
   - Place the ESP32 board straddling the center divider (the gutter) of the breadboard.
   - Insert the left pins of the ESP32 into **Column E, Rows 1 to 15**.
   - Insert the right pins of the ESP32 into **Column F, Rows 1 to 15**.
   - Press down gently until the pins are firmly seated in the breadboard socket holes.

2. **Mounting the LED**:
   - Locate the LED legs. The **longer leg** is the positive terminal (anode), and the **shorter leg** is the negative terminal (cathode).
   - Insert the longer positive leg into **Row 20, Column E**.
   - Insert the shorter negative leg into **Row 21, Column E**. 
   - *Note:* Ensure the two legs are inserted into completely separate rows. Placing them in the same row will short-circuit the LED.

3. **Mounting the Resistor**:
   - Take the 220Ω resistor and bend its legs downward.
   - Insert one leg into **Row 21, Column D** (the same row as the LED's shorter negative leg).
   - Insert the other leg into **Row 25, Column D**.

---

## Wiring Instructions

Use the jumper wires to connect the components on the breadboard to the ESP32 board:

1. **Control Signal Connection**:
   - Take a jumper wire and insert one end into **Row 20, Column A** (connected to the LED's longer positive leg via Row 20).
   - Insert the other end of the wire into the ESP32 pin terminal for **GPIO 2** (located on **Row 11, Column B**).

2. **Ground Connection**:
   - Take a second jumper wire and insert one end into **Row 25, Column A** (connected to the far leg of the resistor via Row 25).
   - Insert the other end of this wire into the ESP32 **GND** pin (located on **Row 14, Column B**).

---

## Programming Section

Below is the code broken down logically into its core sections.

### 1. Variable Declarations

```cpp
// Define the GPIO pin connected to the LED
const int ledPin = 2;
```
*Explanation:* This section declares a constant integer variable named `ledPin` and sets its value to `2`. This informs the program that our LED signal wire is connected to GPIO pin 2 of the ESP32 board.

---

### 2. Setup Function

```cpp
void setup() {
  // Initialize the digital pin as an output
  pinMode(ledPin, OUTPUT);
}
```
*Explanation:* The `setup()` function runs once when the ESP32 powers on or resets. Inside this function, `pinMode(ledPin, OUTPUT);` configures GPIO pin 2 as an output pin, enabling the ESP32 to send voltage to drive the LED.

---

### 3. Loop Function

```cpp
void loop() {
  digitalWrite(ledPin, HIGH); // Turn the LED on
  delay(1000);                // Wait for 1 second (1000 milliseconds)
  
  digitalWrite(ledPin, LOW);  // Turn the LED off
  delay(1000);                // Wait for 1 second (1000 milliseconds)
}
```
*Explanation:* The `loop()` function executes continuously after `setup()` completes. `digitalWrite(ledPin, HIGH);` supplies 3.3V to GPIO pin 2, turning the LED on. `delay(1000);` pauses execution for 1000 milliseconds (1 second). Next, `digitalWrite(ledPin, LOW);` cuts the voltage to 0V, turning the LED off, followed by another 1-second pause. This loop repeats indefinitely, creating a blinking effect.

---

## Uploading the Code

1.

Connect your ESP32 board to your computer using a Micro-USB or USB-C cable.

2. Open the Arduino IDE, microBlock, or your preferred IDE.

3. Select the correct board model (**ESP32 Dev Module**) under **Tools > Board**.

4. Select the appropriate COM port under **Tools > Port**.

5. Click the **Upload** button. If required, hold down the **BOOT** button on your ESP32 board while uploading until the writing process begins.

6. Once uploaded, observe the LED blinking on and off at 1-second intervals.

## Conclusion

Congratulations on completing your first beginner project! You have successfully built a complete circuit on a breadboard, wired an output device to your ESP32, and written code to control hardware timing. You can now move on to explore further beginner projects such as using buzzers, push buttons, and sensors in the next modules.
