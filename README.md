# Beyond-Bits
Beyond Bits enables users to input basic mathematical expressions, processed through analog circuitry to deliver precise results.

## Overview
This project aims to design and implement a system that mimics the functionality of an analog calculator. The system processes different arithmetic expressions on command, performs computations using analog components, and displays the results on a personal computer.

## Features
- Arithmetic Operations: Perform addition, subtraction for numbers ranging from -12 to 12.
- Wireless Input: Inputs can be given wirelessly over a Wi-Fi connection from a phone or laptop, handled by the ESP8266 module.
- Arduino Integration: Input expressions are parsed and evaluated using the Arduino Stack Library and communicated with analog computation circuits.
- Display Output: Calculated results are displayed on the user's personal computer screen via the serial monitor.
- 
## Technical Details

### Hardware Components
- ESP8266: Used for wireless communication to receive mathematical expressions.
- Arduino Uno: Serves as the main microcontroller to parse and forward the expressions.
- Op-Amps, Diodes, Capacitors: Used to build analog circuits for performing arithmetic operations.

### Software Components
- Arduino IDE: For coding and uploading programs to the Arduino Uno and ESP8266.
- Android Studio: For creating an Android app to send mathematical expressions to the ESP8266.
- Stack Library: Used in Arduino for parsing arithmetic expressions.

## Circuit Connections

### ESP8266 to Arduino Uno:
ESP8266 TX to Arduino Uno RX (pin 0)
ESP8266 RX to Arduino Uno TX (pin 1)
Common ground between ESP8266 and Arduino Uno

### Analog Circuits for Arithmetic Operations:
Analog circuits for addition, subtraction using op-amps, resistors, and diodes.
PWM signals generated from analog inputs to perform operations.

## How It Works

1. User Input: The user enters a mathematical expression on a personal computer.
2. Expression Parsing: The computer parses the expression into its constituent numbers, operators, and parentheses.
3. Arduino Interface: The Arduino translates the parsed expression for processing by the analog circuitry.
4. Analog Circuitry: The analog circuitry performs the necessary arithmetic operations.
5. Result Display: The computed result is sent back to the personal computer screen for display.
