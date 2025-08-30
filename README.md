Example Code for Transmitter Program (Using 433 MHz RF Module)
#include <VirtualWire.h> // Include VirtualWire library for RF communication
const int button1Pin = 2; // Pin connected to Button 1 const int button2Pin = 3; // Pin connected to Button 2 const int rfTransmitPin = 12; // Pin for RF transmitter module
void setup() { pinMode(button1Pin, INPUT); // Set Button 1 as input pinMode(button2Pin, INPUT); // Set Button 2 as input
// Initialize RF transmitter vw_set_tx_pin(rfTransmitPin); // Set the RF transmit pin vw_setup(2000); // Set the data rate (baud rate)
}
void loop() { char message[10]; // Buffer for storing transmitted data
// Check if Button 1 is pressed if (digitalRead(button1Pin) == HIGH) { strcpy(message, "FORWARD"); // Set command to move forward vw_send((uint8_t *)message, strlen(message)); // Send the message vw_wait_tx(); // Wait until the message is sent
}
// Check if Button 2 is pressed if (digitalRead(button2Pin) == HIGH) { strcpy(message, "STOP"); // Set command to stop the vehicle vw_send((uint8_t *)message, strlen(message)); // Send the message vw_wait_tx(); // Wait until the message is sent
}
delay(100); // Small delay for stability
}
Example Code for Receiver Program (Using 433 MHz RF Module)
#include <VirtualWire.h> // Include VirtualWire library for RF communication
const int rfReceivePin = 11; // Pin for RF receiver module const int motorPin1 = 5; // Motor control pin 1 (for motor direction) const int motorPin2 = 6; // Motor control pin 2 (for motor direction) const int pwmPin = 9; // PWM pin for controlling speed
void setup() { pinMode(rfReceivePin, INPUT); // Set RF receiver pin as input pinMode(motorPin1, OUTPUT); // Set motor pins as output pinMode(motorPin2, OUTPUT); pinMode(pwmPin,
// Initialize RF receiver vw_set_rx_pin(rfReceivePin); // Set the RF receive pin vw_setup(2000); // Set the data rate (baud rate) vw_rx_start(); // Start receiving data
}
void loop() { uint8_t message[10]; // Buffer for storing received message uint8_t len = sizeof(message); // Length of received message
// Check if data is available from the transmitter if (vw_get_message(message, &len)) {
// Process received message
String command = String((char*)message); // Convert message to string
if (command == "FORWARD") {
digitalWrite(motorPin1, HIGH); // Set motor direction forward digitalWrite(motorPin2, LOW); analogWrite(pwmPin, 255); // Set motor speed to maximum
}
else if (command == "STOP") { digitalWrite(motorPin1, LOW); // Stop the motor digitalWrite(motorPin2, LOW); analogWrite(pwmPin, 0); // Set motor speed to 0 (stop)
}
else if (command == "SPEED UP") { int currentSpeed = analogRead(pwmPin); // Read current speed if (currentSpeed < 255) { analogWrite(pwmPin, currentSpeed + 10); // Increase speed by 10
}
}
else if (command == "SLOW DOWN") { int currentSpeed = analogRead(pwmPin); // Read current speed if (currentSpeed > 0) { analogWrite(pwmPin, currentSpeed - 10); // Decrease speed by 10
   }
  }
 }
}
