

---

# ESP32 Parking Assistance System

This project is designed to help users park their vehicles in a garage or tight space using an ESP32 microcontroller connected to an ultrasonic distance sensor. The system provides real-time visual and audible feedback to ensure safe parking without collision.

## Table of Contents

- [Features](#features)
- [Components](#components)
- [How It Works](#how-it-works)
- [Circuit Diagram](#circuit-diagram)
- [Setup](#setup)
- [Installation](#installation)
- [Usage](#usage)
- [Code](#code)
- [Contributing](#contributing)
- [License](#license)

## Features

- Real-time distance measurements displayed on the app.
- Graphical representation of the vehicle in relation to garage walls or obstacles.
- Audible and visual alerts when the vehicle is too close to an object.
- User authentication to access and save vehicle profiles and settings.

## Components

- ESP32 DevKitC
- Ultrasonic Sensor (HC-SR04)
- Buzzer
- LEDs (2x)
- Resistors (220Ω for LEDs)
- Breadboard and Jumper Wires

## How It Works

The ESP32 Parking Assistance System uses an ultrasonic sensor to measure the distance between the vehicle and nearby obstacles. Here’s a step-by-step explanation of how the system works:

1. **Distance Measurement:**
   - The ultrasonic sensor (HC-SR04) sends out an ultrasonic pulse and waits for it to reflect back from an obstacle.
   - It measures the time taken for the echo to return and calculates the distance based on this time interval.

2. **Data Processing:**
   - The ESP32 reads the duration from the sensor and computes the distance in centimeters.
   - The calculated distance is then compared to a predefined threshold to determine if the vehicle is too close to an obstacle.

3. **Visual and Audible Alerts:**
   - If the measured distance is less than the threshold, the system activates visual and audible alerts.
   - LEDs are used to provide visual feedback. When the vehicle is too close, the LEDs turn on.
   - A buzzer provides an audible alert, sounding when the vehicle is within the critical distance.

4. **Real-time Feedback:**
   - The system continuously measures the distance and updates the alerts in real-time, ensuring the driver is constantly aware of the vehicle’s proximity to obstacles.

5. **User Interface:**
   - Although the basic version uses LEDs and a buzzer for feedback, the system can be extended to display real-time data on a mobile app or web interface via WebSocket for enhanced user interaction.

## Circuit Diagram

Here's the connection layout for the single ultrasonic sensor setup:

#### Ultrasonic Sensor (HC-SR04):
- **VCC** to **5V** on ESP32
- **GND** to **GND** on ESP32
- **TRIG** to **GPIO 5** on ESP32
- **ECHO** to **GPIO 18** on ESP32

#### Buzzer:
- **Positive terminal** to **GPIO 22** on ESP32
- **Negative terminal** to **GND** on ESP32

#### LED 1:
- **Anode (long leg)** to **GPIO 23** on ESP32 via **220Ω resistor**
- **Cathode (short leg)** to **GND** on ESP32

#### LED 2:
- **Anode (long leg)** to **GPIO 25** on ESP32 via **220Ω resistor**
- **Cathode (short leg)** to **GND** on ESP32

![Circuit Diagram](circuit_diagram.png)  <!-- Add the actual circuit diagram image file here -->

## Setup

1. **Download and Install Fritzing (for circuit design):**
   - [Fritzing.org](http://fritzing.org/download/)

2. **Set Up the ESP32 Development Environment:**
   - Install [Arduino IDE](https://www.arduino.cc/en/software) or [PlatformIO with Visual Studio Code](https://platformio.org/install/ide?install=vscode).

## Installation

1. **Clone this repository:**
   ```bash
   git clone https://github.com/your-username/esp32-parking-assistance.git
   cd esp32-parking-assistance
   ```

2. **Open the project in Arduino IDE or PlatformIO.**

3. **Install Required Libraries:**
   - For Arduino IDE: Install the "ESP32" board library via the Board Manager.

4. **Connect the ESP32 to your computer via USB.**

5. **Upload the Code:**
   - Select the appropriate board and port.
   - Upload the code to the ESP32.

## Usage

1. **Power on the ESP32.**
2. **Observe the LEDs and listen to the buzzer for distance alerts.**
3. **Monitor real-time distance measurements via the serial monitor in the Arduino IDE or PlatformIO.**

## Code

Here is the main code for the ESP32:

```cpp
#include <Arduino.h>

// Define pins for the ultrasonic sensor
const int trigPin = 5;
const int echoPin = 18;

// Define pins for the LEDs
const int ledPin1 = 23;
const int ledPin2 = 25;

// Define pin for the buzzer
const int buzzerPin = 22;

// Define the maximum distance threshold in cm
const int maxDistance = 100;

void setup() {
  // Initialize serial communication
  Serial.begin(115200);

  // Initialize pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledPin1, OUTPUT);
  pinMode(ledPin2, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
}

void loop() {
  // Measure distance using the ultrasonic sensor
  long duration, distance;

  // Trigger the ultrasonic sensor
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Read the echo pin and calculate the distance
  duration = pulseIn(echoPin, HIGH);
  distance = (duration / 2) / 29.1;

  // Print the distance to the serial monitor
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  // Check the distance and activate LEDs and buzzer accordingly
  if (distance < maxDistance) {
    digitalWrite(ledPin1, HIGH); // Turn on LED1
    digitalWrite(ledPin2, HIGH); // Turn on LED2
    digitalWrite(buzzerPin, HIGH); // Turn on buzzer
  } else {
    digitalWrite(ledPin1, LOW); // Turn off LED1
    digitalWrite(ledPin2, LOW); // Turn off LED2
    digitalWrite(buzzerPin, LOW); // Turn off buzzer
  }

  // Wait for a short period before the next measurement
  delay(500);
}
```

## Contributing

1. **Fork the repository.**
2. **Create a new branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes and commit them:**
   ```bash
   git commit -m 'Add some feature'
   ```
4. **Push to the branch:**
   ```bash
   git push origin feature/your-feature-name
   ```
5. **Submit a pull request.**

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---
