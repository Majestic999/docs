# Appendix: Component Guide

This component guide provides a quick reference for the electronic parts included in the STEMAIDE Coder-Kit. Use it to understand their functions, identify their pins, and review safety guidelines.

---

## Microcontroller Board & Prototyping

### Arduino Uno R3
*   **Description**: The "brain" of your projects. It contains a microcontroller chip (ATmega328P) that executes instructions loaded from your computer.
*   **Operating Voltage**: 5V (powered via USB or 7-12V external jack).
*   **Pins**: 
    *   **Digital Pins 0-13**: Binary input/output pins. Pins marked with `~` support PWM (analog-like) output.
    *   **Analog Pins A0-A5**: Read continuous voltage values (0V to 5V) and convert them to numbers (0 to 1023).
    *   **Power Pins**: 5V, 3.3V, GND (Ground - 0V return reference), and Reset.

### Breadboard
*   **Description**: A solderless prototyping grid used to temporarily connect components and wires.
*   **Internal Connections**:
    *   **Outer Rails (+ and -)**: Connected vertically down the entire length of the board. Used for distributing power (5V) and Ground (GND).
    *   **Inner Rows (1 to 30 or 60)**: Five holes in a row (columns A to E, and F to J) are connected horizontally. Inserting wires into the same row links them electrically.
    *   **Center Channel**: A divider separating columns E and F. It isolates the two halves of the board, allowing you to mount integrated circuits or push buttons without shorting their opposite pins.

---

## Inputs & Sensors

### Push Button (Tactile Switch)
*   **Description**: A simple momentary switch that completes a circuit when pressed.
*   **Usage**: Digital input (ON/OFF).
*   **Safety Tip**: Use the internal `INPUT_PULLUP` resistor pin mode to prevent "floating" inputs (erratic signals caused by electromagnetic noise).

### Light Dependent Resistor (LDR)
*   **Description**: A photoresistor whose electrical resistance decreases when light shines on it.
*   **Usage**: Analog sensor to detect ambient brightness.
*   **Wiring**: Wired as a voltage divider using a 10kΩ resistor, converting resistance changes into readable voltage levels on analog pin A0.

### HC-SR04 Ultrasonic Distance Sensor
*   **Description**: Emits high-frequency sound pulses and measures the time it takes for the echo to return to calculate distance.
*   **Pins**:
    *   **VCC**: Connects to 5V.
    *   **Trig**: Sends trigger pulse (OUTPUT).
    *   **Echo**: Outputs a pulse width matching echo time (INPUT).
    *   **GND**: Connects to Ground.
*   **Range**: 2cm to 400cm.

### Sound Sensor Module
*   **Description**: A microphone module that detects ambient sound pressure.
*   **Pins**:
    *   **AO**: Analog output (indicates real-time sound amplitude).
    *   **DO**: Digital output (goes HIGH if sound level crosses a potentiometer-adjusted threshold).
    *   **VCC & GND**: Power connection (5V).

---

## Outputs & Actuators

### Light Emitting Diode (LED)
*   **Description**: A semiconductor light source that only conducts current in one direction (polarity sensitive).
*   **Identification**:
    *   **Anode (Positive)**: Longer leg. Connects to the Arduino control pin.
    *   **Cathode (Negative)**: Shorter leg. Connects to GND.
*   **Resistor Requirement**: Always connect a 220Ω resistor in series to limit current and prevent the LED from burning out.

### RGB LED Module
*   **Description**: A combined package containing Red, Green, and Blue LEDs sharing a common pin.
*   **Type**: Common Cathode (common pin connects to GND).
*   **Usage**: Connect R, G, and B pins to PWM pins on the Arduino through individual 220Ω resistors to control and mix colors.

### Traffic Light Module
*   **Description**: A custom PCB featuring Red, Yellow, and Green LEDs aligned in a vertical pillar.
*   **Pins**: Four pins labeled GND (common cathode), R, Y, and G. Connects directly to Arduino output pins without needing external breadboard resistors (current-limiting resistors are built directly into the module board).

### Piezo Buzzer
*   **Description**: An audio output device that produces sound alerts.
*   **Polarity**: The positive lead (usually marked with a `+` symbol or longer leg) connects to the control pin. The negative lead connects to GND.

### SG90 Servo Motor
*   **Description**: A rotary actuator that can turn precisely to any angle between 0 and 180 degrees.
*   **Wired Connections**:
    *   **Brown Wires**: Ground (GND).
    *   **Red Wires**: Power (5V).
    *   **Orange Wires**: Signal (PWM commands from Arduino).
