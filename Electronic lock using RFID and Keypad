#include <Wire.h>           // Include the Wire library for I2C communication
#include <LiquidCrystal.h>  // Include the LiquidCrystal library for controlling the LCD
#include <Keypad.h>         // Include the Keypad library for reading the keypad input
#include <MFRC522.h>        // Include the MFRC522 library for reading RFID cards

// LCD setup
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);  // Create an instance of the LiquidCrystal class using the defined pins
byte numRows = 2;                       // Define the number of rows on the LCD
byte numCols = 16;                      // Define the number of columns on the LCD

// Keypad setup
const byte ROWS = 4;       // Define the number of rows on the keypad
const byte COLS = 4;       // Define the number of columns on the keypad
char keys[ROWS][COLS] = {  // Define the characters on each key of the keypad
  { '1', '2', '3', 'A' },
  { '4', '5', '6', 'B' },
  { '7', '8', '9', 'C' },
  { '*', '0', '#', 'D' }
};
byte rowPins[ROWS] = { 45, 44, 43, 42 };                        // Define the pins for the rows on the keypad
byte colPins[COLS] = { 41, 40, 39, 38 };                        // Define the pins for the columns on the keypad
Keypad keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);  // Create an instance of the Keypad class using the defined pins and keys

// RFID setup
#define SS_PIN 53               // Define the Slave Select pin for the MFRC522
#define RST_PIN 9               // Define the reset pin for the MFRC522
MFRC522 rfid(SS_PIN, RST_PIN);  // Create an instance of the MFRC522 class using the defined pins

// LEDs and buzzer setup
const int redLed = 10;   // Define the pin for the red LED
const int greenLed = 8;  // Define the pin for the green LED
const int buzzer = 7;    // Define the pin for the buzzer

// Relay setup
const int relayPin = 6;       // Define the pin for the relay
const int unlockTime = 3000;  // Define the time in milliseconds to keep the lock unlocked (3 seconds)

// Valid code and RFID tag
String validCode = "123A";          // Define the valid passcode
String validTagID = "69 32 57 59";  //unvalid ID: 69 32 57 59 valid ID: 49 3F B6 42  // Define the valid RFID tag ID

void setup() {
  Serial.begin(9600);  // Initialize the serial communication with a baud rate of 9600

  // Initialize RFID
  SPI.begin();      // Initialize the SPI bus
  rfid.PCD_Init();  // Initialize the MFRC522

  // Initialize LCD
  lcd.begin(numCols, numRows);  // Initialize the LCD with the defined number of rows and columns
  lcd.clear();

  // Initialize LEDs, buzzer, and relay
  pinMode(redLed, OUTPUT);    // Set the red LED pin as an output
  pinMode(greenLed, OUTPUT);  // Set the green LED pin as an output
  pinMode(buzzer, OUTPUT);    // Set the buzzer pin as an output
  pinMode(relayPin, OUTPUT);  // Set the relay pin as an output
  digitalWrite(relayPin, HIGH);
  lcd.print("Place RFID card");  // Display "Place RFID card" on the LCD
  lcd.setCursor(0, 1);           // Move the cursor to the second row of the LCD
  lcd.print("or enter code");    // Display "or enter code" on the LCD

  Serial.println("Place RFID card or enter code:");  // Print "Place RFID card or enter code:" to the serial monitor
}

void loop() {
  String enteredCode = "";  // Initialize an empty string for the entered code

  // Check for keypad input
  while (enteredCode != validCode) {         // Continue looping until a valid code is entered
    char key = keypad.getKey();              // Read the key pressed on the keypad
    if (key != NO_KEY) {                     // If a key was pressed
      if (key == 'C') {                      // If the "C" key was pressed
        enteredCode = "";                    // Clear the entered code
        lcd.clear();                         // Clear the LCD display
        lcd.print("Passcode cleared");       // Display "Passcode cleared" on the LCD
        Serial.println("Passcode cleared");  // Print "Passcode cleared" to the serial monitor
        delay(1000);                         // Wait for 1 second
        lcd.clear();                         // Clear the LCD display
        lcd.print("Enter code:");            // Display "Enter code:" on the LCD
        Serial.println("Enter code:");       // Print "Enter code:" to the serial monitor
      } else {
        enteredCode += key;                 // Add the pressed key to the entered code
        lcd.clear();                        // Clear the LCD display
        lcd.print("Code: " + enteredCode);  // Display "Code: " followed by the entered code on the LCD
        Serial.print(key);                  // Print the pressed key to the serial monitor
      }
    }
    if (enteredCode.length() == validCode.length()) {  // If the entered code has the same length as the valid code
      if (enteredCode == validCode) {                  // If the entered code is the same as the valid code
        openDoor();                                    // Call the openDoor() function
      } else {
        invalidAccess();  // Call the invalidAccess() function
      }
      enteredCode = "";  // Clear the entered code
      break;             // Exit the loop
    }

    // Check for RFID card
    if (rfid.PICC_IsNewCardPresent() && rfid.PICC_ReadCardSerial()) {                               // If a new RFID card is present and read
      String tagID = "";                                                                            // Initialize an empty string for the tag ID
      for (byte i = 0; i < rfid.uid.size; i++) {                                                    // Loop through the card's UID
        tagID += String(rfid.uid.uidByte[i] < 0x10 ? "0" : "") + String(rfid.uid.uidByte[i], HEX);  // Add the byte to the tag ID string
        if (i < rfid.uid.size - 1) {                                                                // If not the last byte
          tagID += " ";                                                                             // Add a space to the tag ID string
        }
      }
      tagID.toUpperCase();  // Convert the tag ID to uppercase

      if (tagID == validTagID) {  // If the tag ID is valid
        openDoor();               // Call the openDoor() function
      } else {
        invalidAccess();  // Call the invalidAccess() function
      }
      rfid.PICC_HaltA();       // Stop the card's communication
      rfid.PCD_StopCrypto1();  // Stop the encryption on the card
    }
  }
}

void openDoor() {
  digitalWrite(greenLed, HIGH);        // Turn on the green LED
  digitalWrite(redLed, LOW);           // Turn off the red LED
  tone(buzzer, 1000);                  // Play a 1000 Hz tone on the buzzer
  delay(100);                          // Wait for 0.1 second
  noTone(buzzer);                      // Turn off the buzzer
  lcd.clear();                         // Clear the LCD display
  lcd.print("Access granted");         // Display "Access granted" on the LCD
  Serial.println("\nAccess granted");  // Print "Access granted" to the serial monitor

  // Activate the relay to unlock the electronic lock
  digitalWrite(relayPin, LOW);
  delay(unlockTime);             // Keep the relay activated for the specified unlock time
  digitalWrite(relayPin, HIGH);  // Deactivate the relay
  resetSystem();                 // Call the resetSystem() function
}

void invalidAccess() {
  digitalWrite(redLed, HIGH);         // Turn on the red LED
  digitalWrite(greenLed, LOW);        // Turn off the green LED
  tone(buzzer, 2000);                 // Play a 2000 Hz tone on the buzzer
  delay(100);                         // Wait for 0.1 second
  noTone(buzzer);                     // Turn off the buzzer
  lcd.clear();                        // Clear the LCD display
  lcd.print("Access denied");         // Display "Access denied" on the LCD
  Serial.println("\nAccess denied");  // Print "Access denied" to the serial monitor
  delay(3000);                        // Wait for 3 seconds
  resetSystem();                      // Call the resetSystem() function
}

void resetSystem() {
  digitalWrite(redLed, LOW);                         // Turn off the red LED
  digitalWrite(greenLed, LOW);                       // Turn off the green LED
  lcd.clear();                                       // Clear the LCD display
  lcd.print("Place RFID card");                      // Display "Place RFID card" on the LCD
  lcd.setCursor(0, 1);                               // Move the cursor to the second row of the LCD
  lcd.print("or enter code");                        // Display "or enter code" on the LCD
  Serial.println("Place RFID card or enter code:");  // Print "Place RFID card or enter code:" to the serial monitor
}
