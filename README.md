🏠 Touch-Based Smart Home Control and Monitoring System (ESP32)
Overview
This project demonstrates a touchscreen-based smart home control panel using the ESP32 microcontroller and a capacitive TFT display. It allows users to control home appliances through touch inputs and monitor environmental conditions in real time.
The system combines manual touch-based control with automatic sensor-based automation, similar to real-world smart home HMIs.

✨ Features
Touch-based control of:
Room Light
Fan
Night Lamp
Automatic room lighting using PIR motion sensor

Real-time monitoring of:
Temperature
Humidity
Ambient light level
Motion status
Clean portrait-mode HMI layout
Improved touch accuracy using averaging and debounce

🧰 Hardware Components Used
ESP32 Development Board
ILI9341 Capacitive Touch TFT Display (FT6206)
DHT22 Temperature & Humidity Sensor
PIR Motion Sensor
LDR (Light Dependent Resistor)
LEDs (to simulate appliances)
Jumper wires

🛠️ Software & Libraries
Arduino IDE / Arduino Framework
Adafruit GFX Library
Adafruit ILI9341 Library
Adafruit FT6206 Touch Library
DHT Sensor Library

⚙️ Working Principle
Users interact with on-screen buttons using capacitive touch.
Touch inputs are processed by the ESP32 to control connected devices.
Sensor data is continuously read and displayed on the screen.
When motion is detected by the PIR sensor, the room light automatically turns ON.
Touch accuracy is enhanced using touch-point averaging and software debounce.

🧪 Simulation
This project is designed and tested using Wokwi Simulator, making it easy to run without physical hardware.
Touch input, sensors, and output devices are simulated for learning and demonstration purposes.

🎯 Applications
Smart Home Automation
Embedded Systems Learning
Human–Machine Interface (HMI) Design
IoT Prototyping
