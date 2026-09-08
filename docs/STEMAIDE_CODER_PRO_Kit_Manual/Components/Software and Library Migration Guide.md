# Software and Library Migration Guide

## 1. Executive Summary & Canonical API Target

This guide details the code, library, and API transformations required to migrate software sketches from the 8-bit AVR Arduino UNO R3 platform to the **Espressif ESP32-DevKitC V4** [E1/E2].

### The Canonical API Target: **ESP32 Arduino Core 3.x**
To establish a modern, long-term educational foundation, all active code examples in the STEMAIDE Coder Pro Kit are standardized on **ESP32 Arduino Core 3.x** (based on ESP-IDF v5.1+) [E4].

This guide centralizes all architectural differences, providing:
1. Canonical, clean Core 3.x implementations.
2. Historical migration mappings from AVR Arduino UNO R3.
3. Backward-compatibility bridges for legacy ESP32 Core 2.x environments.

---

## 2. Analog-to-Digital Converter (ADC) Architecture

### 2.1 Resolution & Dynamic Range
- **Arduino UNO R3**: 10-bit resolution ($2^{10} = 1024$ levels), measuring $0\text{ to }5.0\text{ V}$. Step size $\approx 4.88\text{ mV}$ [E1].
- **ESP32-DevKitC V4**: 12-bit resolution ($2^{12} = 4096$ levels), measuring $0\text{ to }3.3\text{ V}$ at default $11\text{ dB}$ attenuation. Step size $\approx 0.81\text{ mV}$ [E1].

### 2.2 Conversion Formulas & Voltage Qualification
In beginner educational contexts, a linear approximation is often used:

$$\text{Approximation (Educational Only):} \quad V \approx \frac{\text{rawADC}}{4095.0} \times 3.3\text{ V} \quad [\text{E5}]$$

> [!WARNING]
> **ADC Non-Linearity Qualification**:
> Raw ADC values on the ESP32 do NOT exhibit perfect linearity across the full $0\text{–}3.3\text{V}$ range. The internal SAR ADC possesses a deadband below $\approx 100\text{ mV}$ and exhibits saturation curvature above $\approx 3.1\text{ V}$ [E1/E4]. For precision analog measurements, software MUST use the factory-calibrated `analogReadMilliVolts()` API [E4].

### 2.3 Code Migration: Analog Voltage Reading

#### Legacy Arduino UNO R3 (AVR)
```cpp
const int sensorPin = A0;
void setup() { Serial.begin(9600); }
void loop() {
  int raw = analogRead(sensorPin);
  float voltage = raw * (3.3 / 4095.0);
  Serial.println(voltage);
  delay(500);
}
```

#### Canonical ESP32 Arduino Core 3.x
```cpp
const int sensorPin = 34; // Dedicated ADC1 pin (Wi-Fi concurrent)

void setup() {
  Serial.begin(115200);
  // Default is 12-bit (0-4095) with 11 dB attenuation (~0 to 3.3V)
  analogSetAttenuation(ADC_11db);
}

void loop() {
  // Method 1: Calibrated Millivolts (Recommended for accuracy)
  uint32_t mv = analogReadMilliVolts(sensorPin);
  float voltage = mv / 1000.0;

  // Method 2: Raw 12-bit Quantization
  int raw = analogRead(sensorPin);

  Serial.printf("Raw ADC: %d | Calibrated Voltage: %.3f V\n", raw, voltage);
  delay(500);
}
```

---

## 3. Pulse-Width Modulation (LEDC) Architecture

### 3.1 Architectural Shift
On the Arduino UNO R3, PWM was generated via `analogWrite(pin, val)` using fixed 8-bit hardware timers tied to specific pins (D3, D5, D6, D9, D10, D11) [E1].

The ESP32 features a dedicated **LEDC (LED Control) hardware peripheral** with 16 independent channels, customizable frequencies (from 1 Hz to 40 MHz), and configurable resolution (from 1-bit to 16-bit) routable to any general-purpose output pin [E1/E4].

### 3.2 Evolution of the ESP32 PWM API

```
  AVR Arduino UNO R3          ESP32 Core 2.x (Legacy)          ESP32 Core 3.x (Canonical)
 ┌───────────────────┐       ┌─────────────────────────┐       ┌────────────────────────┐
 │ analogWrite(pin)  │  ──►  │ ledcSetup(ch, freq, res)│  ──►  │ ledcAttach(pin, freq,  │
 │                   │       │ ledcAttachPin(pin, ch)  │       │            resolution) │
 │ (Fixed 8-bit,     │       │ ledcWrite(ch, duty)     │       │ ledcWrite(pin, duty)   │
 │  490/980 Hz)      │       │ (Manual channel binding)│       │ (Pin-centric binding)  │
 └───────────────────┘       └─────────────────────────┘       └────────────────────────┘
```

### 3.3 Code Migration: LED Dimming / DC Motor Speed

#### Legacy Arduino UNO R3 (AVR)
```cpp
const int motorPin = 3;
void setup() { pinMode(motorPin, OUTPUT); }
void loop() {
  analogWrite(motorPin, 128); // 50% duty cycle (0-255)
}
```

#### Canonical ESP32 Arduino Core 3.x
```cpp
const int motorPin = 25;
const int freq = 5000;         // 5 kHz PWM frequency (silent motor drive)
const int resolution = 8;      // 8-bit resolution (0-255)

void setup() {
  // Core 3.x automatically allocates an available hardware channel
  ledcAttach(motorPin, freq, resolution);
}

void loop() {
  ledcWrite(motorPin, 128);    // 50% duty cycle directly by pin number
}
```

#### ESP32 Core 2.x Backward-Compatibility Snippet
```cpp
#if ESP_ARDUINO_VERSION_MAJOR < 3
  const int pwmChannel = 0;
  ledcSetup(pwmChannel, freq, resolution);
  ledcAttachPin(motorPin, pwmChannel);
  ledcWrite(pwmChannel, 128);
#endif
```

---

## 4. Servo Motor Control Migration

### Architectural Incompatibility
The standard AVR `<Servo.h>` library relies on 8-bit Timer1 hardware interrupts specific to the ATmega328P. It fails to compile on the 32-bit Xtensa architecture [E1/E4].

### Migration: `ESP32Servo` Library
The `ESP32Servo` library leverages the ESP32's hardware LEDC timers, allowing glitch-free control of up to 16 independent servos with 14-bit or 16-bit timing precision [E4].

#### Canonical ESP32 Implementation
```cpp
#include <ESP32Servo.h>

Servo myServo;
const int servoPin = 27;

void setup() {
  // Allow allocation of all timers
  ESP32PWM::allocateTimer(0);
  ESP32PWM::allocateTimer(1);
  ESP32PWM::allocateTimer(2);
  ESP32PWM::allocateTimer(3);

  myServo.setPeriodHertz(50);             // Standard 50 Hz servo PWM
  myServo.attach(servoPin, 500, 2400);    // Min pulse 500 us, max pulse 2400 us
}

void loop() {
  myServo.write(90);                      // Sweep to 90 degrees
  delay(1000);
  myServo.write(0);
  delay(1000);
}
```

---

## 5. Serial Communication & Bluetooth Migration

### 5.1 Elimination of `SoftwareSerial`
On the Arduino UNO R3, `SoftwareSerial.h` bit-banged UART communication on digital pins D2/D3 because the single hardware UART was reserved for the USB serial monitor. SoftwareSerial is processor-intensive and highly prone to packet corruption at speeds above 19200 baud [E1].

The ESP32 features **three full hardware UART controllers** with 128-byte hardware FIFO buffers [E1]:
- `Serial` (UART0): Hardwired to CP2102N USB bridge (`GPIO 1 TX0`, `GPIO 3 RX0`).
- `Serial1` (UART1): Available for custom routing.
- `Serial2` (UART2): Default hardware UART on `GPIO 17 (TX2)` and `GPIO 16 (RX2)`.

### 5.2 HC-05 HardwareSerial Migration
The physical HC-05 Bluetooth module connects directly to hardware UART2 at full native speed without software bit-banging:

```cpp
#include <HardwareSerial.h>

HardwareSerial SerialBT(2); // UART2

void setup() {
  Serial.begin(115200);                                  // USB Monitor
  SerialBT.begin(9600, SERIAL_8N1, 16, 17);              // RX2=16, TX2=17
  Serial.println("HC-05 Hardware UART Initialized.");
}

void loop() {
  while (SerialBT.available()) {
    char c = SerialBT.read();
    Serial.write(c);
  }
  while (Serial.available()) {
    char c = Serial.read();
    SerialBT.write(c);
  }
}
```

### 5.3 Native Onboard Bluetooth Classic (Emulating HC-05)
Because the ESP32-WROOM-32D integrates a dual-mode 2.4 GHz radio, students can replace the physical HC-05 module entirely with the onboard radio using `BluetoothSerial.h` [E1/E4]:

```cpp
#include "BluetoothSerial.h"

BluetoothSerial SerialBT;

void setup() {
  Serial.begin(115200);
  SerialBT.begin("STEMAIDE_CoderPro"); // Device name visible to smartphones
  Serial.println("Onboard Bluetooth Classic SPP Ready.");
}

void loop() {
  while (SerialBT.available()) {
    Serial.write(SerialBT.read());
  }
  while (Serial.available()) {
    SerialBT.write(Serial.read());
  }
}
```

---

## 6. Comprehensive Library Replacement Reference

| Module / Function | Legacy AVR Library | Recommended ESP32 Core 3.x Library | Installation / Source | Notes |
|---|---|---|---|---|
| **Servo Motors** | `<Servo.h>` | `<ESP32Servo.h>` by Kevin Harrington | Arduino Library Manager | Hardware LEDC PWM timer allocation [E4] |
| **IIC 1602 LCD** | `<LiquidCrystal_I2C.h>` | `<LiquidCrystal_I2C.h>` by Frank de Brabander | Arduino Library Manager | Fully compatible with ESP32 `Wire` [E4] |
| **RFID RC522** | `<MFRC522.h>` | `<MFRC522.h>` by GithubCommunity | Arduino Library Manager | Uses hardware VSPI (`SPI.h`) [E4] |
| **BME280 Sensor** | `<Adafruit_BME280.h>` | `<Adafruit_BME280.h>` | Arduino Library Manager | Supports hardware I²C and SPI [E4] |
| **DS18B20 Temp** | `<OneWire.h>`, `<DallasTemperature.h>` | `<OneWire.h>`, `<DallasTemperature.h>` | Arduino Library Manager | Fully ported to ESP32 architecture [E4] |
| **Infrared RX** | `<IRremote.h>` v2/v3 | `<IRremote.h>` v4.3+ or `<IRremoteESP8266.h>` | Arduino Library Manager | Uses hardware RMT peripheral [E4] |
| **8x8 Matrix** | `<LedControl.h>` | `<MD_MAX72xx.h>` or `<LedControl.h>` | Arduino Library Manager | Hardware VSPI or direct GPIO bitbang [E4] |
| **Stepper Motor** | `<Stepper.h>` | `<Stepper.h>` or `<AccelStepper.h>` | Arduino Library Manager | Supports 4-wire half-step & full-step [E4] |
