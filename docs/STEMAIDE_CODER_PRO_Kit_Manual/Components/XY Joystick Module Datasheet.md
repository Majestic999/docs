# XY Dual-Axis Joystick Module — Technical Datasheet

## General Description

A dual-axis analog joystick module based on two orthogonal 10 kΩ potentiometers mounted at 90° to measure horizontal (X-axis) and vertical (Y-axis) deflection, paired with an integrated momentary tactile pushbutton switch activated by depressing the joystick handle (Z-axis select) [E1].

### 3.3V Terminal Connection Mandate
> [!CAUTION]
> **Strict 3.3V Connection**:
> The joystick's `VCC` pin must connect strictly to the **`3V3` rail** (NOT 5V!). Connecting VCC to 5V causes the X and Y potentiometer wipers to output voltages up to 5.0V, permanently damaging the ESP32 ADC inputs [E1/E5]!

### Wi-Fi Concurrent ADC1 Allocation
Both potentiometer wipers are mapped directly to input-only ADC1 pins:
- **VRx (X-Axis)**: Connects to **`GPIO 36` (SENSOR_VP / ADC1_CH0)** [E5].
- **VRy (Y-Axis)**: Connects to **`GPIO 39` (SENSOR_VN / ADC1_CH3)** [E5].
- **SW (Z-Button)**: Connects to **`GPIO 13`** with internal pull-up (`INPUT_PULLUP`) [E5].

## Specifications

| Parameter | Value | Evidence Tier |
|---|---|---|
| **Potentiometer Values** | Dual 10 kΩ linear taper potentiometers (X and Y axes) | [E1] |
| **Operating Voltage ($V_{CC}$)**| **3.3 V DC (from ESP32 3V3 Rail)** | [E5] |
| **Current Consumption** | $I = \frac{3.3\text{V}}{5\text{ k}\Omega \text{ equiv}} \approx 0.66\text{ mA}$ total | [E5] |
| **Operating Deflection Angle** | ±30° from center in X and Y planes | [E1] |
| **Center Rest Voltage** | ~1.65 V nominal (Raw ADC count ~2048 at 12-bit resolution) | [E5] |
| **Pushbutton Switch** | Momentary SPST-NO tactile switch (Active LOW) | [E1] |
| **Target Microcontroller Pins**| **VRx: GPIO 36 (ADC1) | VRy: GPIO 39 (ADC1) | SW: GPIO 13** | [E5] |

## Pinout & Wiring

| Pin Label | Function | ESP32 Connection | Description |
|---|---|---|---|
| **GND** | Ground | **GND** | Common ground reference [E1] |
| **+5V / VCC**| Power Supply | **3V3 Rail** | **Connect to 3.3V** (Do NOT connect to 5V!) [E5] |
| **VRx** | X-Axis Potentiometer | **GPIO 36 (ADC1_CH0)**| Analog voltage (0–3.3V, Center ~1.65V) [E5] |
| **VRy** | Y-Axis Potentiometer | **GPIO 39 (ADC1_CH3)**| Analog voltage (0–3.3V, Center ~1.65V) [E5] |
| **SW** | Pushbutton Switch | **GPIO 13** | Digital input with `INPUT_PULLUP` [E5] |

```
   ESP32-DevKitC V4                      XY Joystick Module
  ┌────────────────┐                     ┌───────────────────┐
  │            3V3 ├────────────────────►│ +5V (VCC)         │ (Connected to 3.3V!)
  │   GPIO 36 (VP) ├◄────────────────────┤ VRx (X-Axis)      │
  │   GPIO 39 (VN) ├◄────────────────────┤ VRy (Y-Axis)      │
  │        GPIO 13 ├◄────────────────────┤ SW  (Z-Switch)    │
  │            GND ├─────────────────────┤ GND               │
  └────────────────┘                     └───────────────────┘
```

## ESP32 Sample Code (Core 3.x) — Dual-Axis Controller

```cpp
const int xPin = 36; // ADC1_CH0 (Input only)
const int yPin = 39; // ADC1_CH3 (Input only)
const int swPin = 13; // Tactile switch with pull-up

void setup() {
  Serial.begin(115200);
  pinMode(swPin, INPUT_PULLUP);
  analogSetAttenuation(ADC_11db); // 0-3.3V scale
  Serial.println("ESP32 XY Joystick Controller Initialized.");
}

void loop() {
  int rawX = analogRead(xPin);
  int rawY = analogRead(yPin);
  uint32_t mvX = analogReadMilliVolts(xPin);
  uint32_t mvY = analogReadMilliVolts(yPin);
  int swPressed = (digitalRead(swPin) == LOW) ? 1 : 0;

  Serial.printf("X: %4d (%4u mV) | Y: %4d (%4u mV) | Button: %s\n",
                rawX, mvX, rawY, mvY, swPressed ? "PRESSED" : "RELEASED");
  delay(100);
}
```

## Source References
- ALPS Alpine RKJXK ThumbPointer Joystick Datasheet: https://www.alpsalpine.com
