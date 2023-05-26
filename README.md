#include <LiquidCrystal.h>

// Comment section:

// This Arduino code demonstrates an On-Off control system that uses a setpoint, a process variable (PV), and a control output (CO) to regulate an actuator (e.g., a motor or a heater).
// The code also utilizes an LCD display and buttons to interact with the control system and display relevant information.

int sensor = A0;
int incButton = 2;
int decButton = 3;
int actuator = 6;
int PV = 0;
int setPoint = 0;
int CO = 0;
int HYS = 30; // Hysteresis value
int Error = 0;
int button1 = 4; // Button to cycle through display options
int button2 = 5; // Button to cycle through display options
int displayIndex = 0; // Index to track the current display option on the LCD

LiquidCrystal lcd(12, 11, 10, 9, 8, 7); // Initialize the LCD display with the corresponding pins

void setup() {
pinMode(actuator, OUTPUT);
pinMode(incButton, INPUT_PULLUP);
pinMode(decButton, INPUT_PULLUP);
pinMode(button1, INPUT_PULLUP);
pinMode(button2, INPUT_PULLUP);
Serial.begin(9600);
lcd.begin(16, 2);
}

void loop() {
PV = analogRead(sensor);
Error = setPoint - PV;

if (PV <= (setPoint-HYS)) {
CO = 255; // Turn on the actuator at full power if PV is below (setPoint - Hysteresis)
} else if (PV >= (setPoint+HYS)) {
CO = 0; // Turn off the actuator if PV is above (setPoint + Hysteresis)
}

analogWrite(actuator, CO); // Apply the control output (CO) to the actuator

if (digitalRead(incButton) == LOW) {
if (displayIndex == 0) setPoint += 10; // Increase the setpoint by 10 if in setpoint display mode
else if (displayIndex == 1) CO = 255; // Turn on the actuator at full power if in control output display mode
else if (displayIndex == 2) PV += 10; // Increase the process variable by 10 if in PV display mode
else if (displayIndex == 3) Error += 10; // Increase the error by 10 if in error display mode
else if (displayIndex == 4) HYS += 10; // Increase the hysteresis value by 10 if in hysteresis display mode
delay(200); // Debounce the button
} else if (digitalRead(decButton) == LOW) {
if (displayIndex == 0) setPoint -= 10; // Decrease the setpoint by 10 if in setpoint display mode
else if (displayIndex == 1) CO = 0; // Turn off the actuator if in control output display mode
else if (displayIndex == 2) PV -= 10; // Decrease the process variable by 10 if in PV display mode
else if (displayIndex == 3) Error -= 10; // Decrease the error by 10 if in error display mode
else if (displayIndex == 4) HYS -= 10; // Decrease the hysteresis value by 10 if in hysteresis display mode
delay(200); // Debounce the button
  }
  
  if (digitalRead(button1) == LOW) { // Check if the increase button is pressed
    displayIndex++;
    if (displayIndex > 4) displayIndex = 0;
    delay(200); // Debounce the button
  }
  
  if (digitalRead(button2) == LOW) { // Check if the decrease button is pressed
    displayIndex--;
    if (displayIndex < 0) displayIndex = 4;
    delay(200); // Debounce the button
  }
  
  switch (displayIndex) {
    case 0:
      lcd.clear(); // Clear the LCD display
      lcd.setCursor(0, 0);
      lcd.print("SP= ");
      lcd.print(setPoint);
      break;
    case 1:
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("CO= ");
      lcd.print(CO);
      break;
    case 2:
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("PV= ");
      lcd.print(PV);
      break;
    case 3:
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Error= ");
      lcd.print(Error);
      break;
    case 4:
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("HYS= ");
      lcd.print(HYS);
      break;
  }
  // Serial communication for debugging
  Serial.print("SP = ");
  Serial.print(setPoint);
  Serial.print("\t CO = ");
  Serial.print(CO);
  Serial.print("\t PV = ");
  Serial.print(PV);
  Serial.print("\t Error = ");
  Serial.print(Error);
  Serial.println();
}
