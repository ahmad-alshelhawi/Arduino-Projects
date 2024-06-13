#include <Servo.h>          // standard library for the servo
#include <NewPing.h>        // for the Ultrasonic sensor function library

char t = 0;
int ipin = 3;
float rpm;
volatile long p;
unsigned long T;
unsigned int ppt = 20;
float v;
int distanceRight = 0;
int distanceLeft = 0;

// Motor A connections
int enA = 11;
int in1 = 52;
int in2 = 51;

// Motor B connections
int enB = 6;
int in3 = 50;
int in4 = 49;

// Motor D connections
int enC = 4;
int in5 = 43;
int in6 = 44;

// Motor C connections
int enD = 5;
int in7 = 45;
int in8 = 47;

int speedCar = 65;     // 50 - 255
int speedCar1 = 70;

// sensor pins
#define trig_pin 8
#define echo_pin 3
#define maximum_distance 400

boolean goesForward = false;
int distance = 100;

void counter() {
  p++;
}

NewPing sonar(trig_pin, echo_pin, maximum_distance); // sensor function
Servo servo_motor;

void setup() {
  Serial.begin(9600);
  pinMode(ipin, INPUT);
  attachInterrupt(0, counter, FALLING);
  p = 0;
  rpm = 0;
  T = 0;
  distanceRight = 0;
  distanceLeft = 0;
  delay(50);

  pinMode(enA, OUTPUT);
  pinMode(enB, OUTPUT);
  pinMode(enC, OUTPUT);
  pinMode(enD, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  pinMode(in5, OUTPUT);
  pinMode(in6, OUTPUT);
  pinMode(in7, OUTPUT);
  pinMode(in8, OUTPUT);

  // Turn off motors - Initial state
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  digitalWrite(in5, LOW);
  digitalWrite(in6, LOW);
  digitalWrite(in7, LOW);
  digitalWrite(in8, LOW);

  servo_motor.attach(9); // our servo pin
  servo_motor.write(90);
  delay(500);

  // Read initial distances
  for (int i = 0; i < 4; i++) {
    distance = readPing();
    delay(50);
  }
}

void loop() {
  // Calculate RPM and velocity
  detachInterrupt(0);
  rpm = (p * 6) / ppt;
  v = 2 * 3.1415926536 * 3 * rpm / 60;
  p = 0;
  attachInterrupt(0, counter, FALLING);

  // Read distance
  distance = readPing();
  delay(20);

  // Read serial input
  if (Serial.available()) {
    t = Serial.read();
  }

  // Execute command
  switch (t) {
    case 'A':
      autonomousMode();
      break;
    case 'F':
      moveForward1();
      break;
    case 'B':
      moveBackward1();
      break;
    case 'L':
      turnLeft1();
      break;
    case 'R':
      turnRight1();
      break;
    case 'f':
      turnFL();
      break;
    case 'r':
      turnBR();
      break;
    case 'b':
      turnBL();
      break;
    case 'u':
      turnFR();
      break;
    case 'S':
      moveStop1();
      break;
    case 'q':
      stopAndReset();
      break;
    default:
      break;
  }

  // Print status
  Serial.println("*R" + String(rpm) + " RPM" + "*");
  Serial.println("*s" + String(v) + " cm/s" + "*");
  Serial.println("*d" + String(distance) + " Cm" + "*");
  delay(25);
}

void autonomousMode() {
  moveForward();
  if (distance > 25) {
    moveForward();
  } else if (distance <= 33) {
    moveStop();
    delay(500);
    distanceRight = lookRight();
    delay(200);
    distanceLeft = lookLeft();
    delay(200);
    moveBackward();
    delay(350);
    moveStop();
    delay(400);

    if (distance <= distanceLeft && distanceLeft < distanceRight) {
      turnRight();
    } else if (distance <= distanceRight && distanceRight < distanceLeft) {
      turnLeft();
    } else if (distance <= distanceLeft && distanceLeft < distanceRight && distanceRight < distance || distance <= distanceRight && distanceRight < distanceLeft && distanceLeft < distance) {
      moveForward();
    } else if (distance && distanceLeft && distanceRight << 15) {
      moveBackward();
      delay(75);
    }
  }

  distance = readPing();
  delay(50);
}

void stopAndReset() {
  moveStop1();
  delay(50);
  rpm = 0;
  v = 0;
  distance = readPing();
  delay(50);
}

int lookRight() {
  servo_motor.write(0);
  delay(500);
  int distance = readPing();
  delay(100);
  servo_motor.write(90);
  return distance;
}

int lookLeft() {
  servo_motor.write(180);
  delay(500);
  int distance = readPing();
  delay(100);
  servo_motor.write(90);
  return distance;
}

int readPing() {
  delay(50);
  int cm = sonar.ping_cm();
  if (cm == 0) {
    cm = 250;
  }
  return cm;
}

void moveStop() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  digitalWrite(in5, LOW);
  digitalWrite(in6, LOW);
  digitalWrite(in7, LOW);
  digitalWrite(in8, LOW);
  analogWrite(enA, speedCar);
  analogWrite(enB, speedCar);
  analogWrite(enC, speedCar);
  analogWrite(enD, speedCar);
  delay(100);
}

void moveStop1() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  digitalWrite(in5, LOW);
  digitalWrite(in6, LOW);
  digitalWrite(in7, LOW);
  digitalWrite(in8, LOW);
}

void moveForward() {
  if (!goesForward) {
    goesForward = true;
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    analogWrite(enA, speedCar);
    digitalWrite(in3, LOW);
    digitalWrite(in4, HIGH);
    analogWrite(enB, speedCar);
    digitalWrite(in5, LOW);
    digitalWrite(in6, HIGH);
    analogWrite(enC, speedCar);
    digitalWrite(in7, LOW);
    digitalWrite(in8, HIGH);
    analogWrite(enD, speedCar);
  }
}

void moveForward1() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  analogWrite(enA, speedCar);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  analogWrite(enB, speedCar);
  digitalWrite(in5, LOW);
  digitalWrite(in6, HIGH);
  analogWrite(enC, speedCar);
  digitalWrite(in7, LOW);
  digitalWrite(in8, HIGH);
  analogWrite(enD, speedCar);
}

void moveBackward() {
  goesForward = false;
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar);
  digitalWrite(in5, HIGH);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar);
  digitalWrite(in7, HIGH);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar);
  distance = distance - 20;
}

void moveBackward1() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar);
  digitalWrite(in5, HIGH);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar);
  digitalWrite(in7, HIGH);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar);
}

void turnRight() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar1);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  analogWrite(enB, speedCar1);
  digitalWrite(in5, HIGH);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar1);
  digitalWrite(in7, LOW);
  digitalWrite(in8, HIGH);
  analogWrite(enD, speedCar1);
  delay(450);
}

void turnRight1() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  analogWrite(enB, speedCar);
  digitalWrite(in5, HIGH);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar);
  digitalWrite(in7, LOW);
  digitalWrite(in8, HIGH);
  analogWrite(enD, speedCar);
}

void turnLeft() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  analogWrite(enA, speedCar1);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar1);
  digitalWrite(in5, LOW);
  digitalWrite(in6, HIGH);
  analogWrite(enC, speedCar1);
  digitalWrite(in7, HIGH);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar1);
  delay(450);
}

void turnLeft1() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  analogWrite(enA, speedCar);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar);
  digitalWrite(in5, LOW);
  digitalWrite(in6, HIGH);
  analogWrite(enC, speedCar);
  digitalWrite(in7, HIGH);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar);
}

void turnFR() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  analogWrite(enA, speedCar);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar);
  digitalWrite(in5, LOW);
  digitalWrite(in6, HIGH);
  analogWrite(enC, speedCar);
  digitalWrite(in7, LOW);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar);
}

void turnFL() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  analogWrite(enB, speedCar);
  digitalWrite(in5, LOW);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar);
  digitalWrite(in7, LOW);
  digitalWrite(in8, HIGH);
  analogWrite(enD, speedCar);
}

void turnBL() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar);
  digitalWrite(in5, LOW);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar);
  digitalWrite(in7, HIGH);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar);
}

void turnBR() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, speedCar);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  analogWrite(enB, speedCar);
  digitalWrite(in5, HIGH);
  digitalWrite(in6, LOW);
  analogWrite(enC, speedCar);
  digitalWrite(in7, LOW);
  digitalWrite(in8, LOW);
  analogWrite(enD, speedCar);
}
