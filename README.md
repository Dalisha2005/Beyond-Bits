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

#### Adder
![](adder.png)

#### Subtractor
![](subtractor.png)

#### Multiplier
![](multiplier.png)

## How It Works

1. User Input: The user enters a mathematical expression on a personal computer.
2. Expression Parsing: The computer parses the expression into its constituent numbers, operators, and parentheses.
3. Arduino Interface: The Arduino translates the parsed expression for processing by the analog circuitry.
4. Analog Circuitry: The analog circuitry performs the necessary arithmetic operations.
5. Result Display: The computed result is sent back to the personal computer screen for display.

## Arduino Code
```

      #define MAX_STACK_SIZE 100 // Adjust the size according to your needs
      
      // #include <SoftwareSerial.h>
      
      // Define the RX and TX pins for communication with ESP8266
      // #define ESP8266_TX 1   // Arduino RX pin connected to ESP8266 TX pin
      // #define ESP8266_RX 0   // Arduino TX pin connected to ESP8266 RX pin
      
      // Create a SoftwareSerial object for communication with ESP8266
      // SoftwareSerial espSerial(ESP8266_RX, ESP8266_TX);
      
      // int incomingByte = 0;-
      
      template <class T>
      class Stack {
      private:
          T stackArray[MAX_STACK_SIZE];
          int top;
      
      public:
          Stack() {
              top = -1;
          }
      
          void push(T item) {
              if (top >= MAX_STACK_SIZE - 1) {
                  Serial.println("Stack overflow!");
                  return;
              }
              stackArray[++top] = item;
          }
      
          T pop() {
              if (top < 0) {
                  Serial.println("Stack underflow!");
                  return T(); // Return default value for type T
              }
              return stackArray[top--];
          }
      
          T peek() {
              if (top < 0) {
                  Serial.println("Stack is empty!");
                  return T(); // Return default value for type T
              }
              return stackArray[top];
          }
      
          bool isEmpty() {
              return top < 0;
          }
      };
      
      Stack<char> operators; // Stack to hold operators
      Stack<double> operands;   // Stack to hold operands
      
      double operate(double a, double b, char op) {
          switch(op) {
              case '+':
                  // analogWrite(A0, a);
                  // analogWrite(A1, b);
                  return (a+b);
              case '-':
                  // analogWrite(A2, a);
                  // analogWrite(A3, b);
                  return (a-b);
              case '*':
                  // analogWrite(A4, a);
                  // analogWrite(A5, b);
                  return (a*b);
              // case '/':
              //     return a / b;
              // case '^':
              //     return a ^ b;
              // Add more cases for other operators as needed
              default:
                  return 0; // Handle invalid operator
          }
      }
      
      int evaluateExpression(String expression) {
          for(int i = 0; i < expression.length(); i++) {
              char token = expression[i];
              int flag = 0;
              if(isdigit(token)) {
                  double operand1 = 0;
                  double operand2 = 0;
                  while(i < expression.length() && (isdigit(expression[i]) || expression[i] == '.')) {
                    if(!flag && isdigit(expression[i])) operand1 = operand1 * 10 + (expression[i++] - '0');
                    else if(flag && isdigit(expression[i])) operand2 = operand2 * 10 + (expression[i++] - '0');
                    else flag = i++;
                  }
                  i--;
                  if (flag != 0){
                    int c = i - flag;
                    int divi = 1;
                    while(!c){
                      divi *= 10;
                      c--;
                    }
                    operand2 /= divi;
                  }
                  operands.push(operand1 + operand2);
              }
              else if(token == '(') {
                  operators.push(token);
              }
              else if(token == ')') {
                  while(!operators.isEmpty() && operators.peek() != '(') {
                      char op = operators.pop();
                      double b = operands.pop();
                      double a = operands.pop();
                      operands.push(operate(a, b, op));
                  }
                  if(!operators.isEmpty())operators.pop(); // Pop '('
              }
              else if(token == '+' || token == '-' || token == '*' || token == '/') {
                  while(!operators.isEmpty() && precedence(operators.peek()) >= precedence(token)) {
                      char op = operators.pop();
                      double b = operands.pop();
                      double a = operands.pop();
                      operands.push(operate(a, b, op));
                  }
                  operators.push(token);
              }
          }
          while(!operators.isEmpty()) {
              char op = operators.pop();
              double b = operands.pop();
              double a = operands.pop();
              operands.push(operate(a, b, op));
          }
          return operands.pop();
      }
      
      int precedence(char op) {
          switch(op) {
              case '-':
                return 1;
              case '+':
                  return 2;
              case '*':
                return 3;
              // case '/':
              //     return 4;
              // case '^':
              //   return 5;
              // Add more cases for other operators as needed
              default:
                  return 0; // Handle invalid operator
          }
      }
      
      void setup() {
          Serial.begin(115200);
          // Serial.begin(9600);
          // espSerial.begin(9600);
          pinMode(A0, OUTPUT);
          pinMode(A1, OUTPUT);
          pinMode(A2, OUTPUT);
          pinMode(A3, OUTPUT);
          pinMode(A4, OUTPUT);
          pinMode(A5, OUTPUT);
          pinMode(A6, INPUT);
      }
      
      void loop() {
        String expression;
        Serial.println("Uploaded");
        delay(1000);
        if (Serial.available()) { 
          expression = Serial.readStringUntil('\n');  // Process the received expression here 
          Serial.println("Received expression: " + expression); // we will get expression from wifi
        }
          double result = evaluateExpression(expression);
          Serial.print("Result: ");
          Serial.println(result);
      }

```

## NodeMCU Code

```
      #include <ESP8266WiFi.h>
      #include <ESP8266WebServer.h>
      
      const char* ssid = "Redmi Note 11Pro";
      const char* password = "Isha@1706";
      
      ESP8266WebServer server(80);
      
      
      void setup() {
      
      
        Serial.begin(115200);
      
        // Connect to Wi-Fi
        WiFi.begin(ssid, password);
        Serial.print("Connecting to WiFi");
        while (WiFi.status() != WL_CONNECTED) {
          delay(1000);
          Serial.print(".");
        }
        Serial.println("Connected");
        Serial.print("Got IP: ");  
        Serial.println(WiFi.localIP());
      
        // Set up HTTP server routes
        server.on("/calculate", HTTP_POST , handleMathExpression);
      
        // Start server
        server.begin();
        Serial.println("HTTP server started");
      }
      
      void loop() {
        server.handleClient();
      }
      
      void handleMathExpression() {
        if (server.hasArg("expression")) {
          String expression = server.arg("expression");
          Serial.println("Received expression:" + expression);
      
          // Forward the expression to Arduino Uno via serial communication
          Serial1.println(expression);
      
          // Send a success response to the client
          server.send(200, "text/plain", "Expression received and processed");
        }
        else {
          server.send(400, "text/plain", "bad request");
        }
      }

```


