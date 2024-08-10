//Including library
#include <LiquidCrystal.h> 

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

// Defining pin
const int waterpin = A0;       
const int temppin = A1; 
const int npkpin = A2;         

//Digital pin defining
const int wrelaypin = 6;
const int npkrelaypin = 7;

//setting threshold
const int threshold = 300;        

void setup() {
  Serial.begin(9600);

  // defining lcd wiith rows and columns and starting it.
  lcd.begin(16, 2); 
  lcd.print("Initializing System...");

 //Defining relay pin as output pin
  pinMode(wrelaypin, OUTPUT);
  pinMode(npkrelaypin, OUTPUT);

  // setting relay pin at 0 or off after initialization
  digitalWrite(wrelaypin, LOW);
  digitalWrite(npkrelaypin, LOW);

  delay(3500); 
}

void loop() {
  checkSensors();
  //delay of 5500 milisecond
  delay(5500); 
}

void checkSensors() {
  // storing readings of sensors in variable
  int wlevel = analogRead(waterpin);
  int tempreading = analogRead(temppin);
  int npklevel = analogRead(npkpin);

  // converting temperature sensor value to temperature in degree celcius
  float voltage = tempreading * (5.0 / 1023.0); 
  float temp = voltage * 100; 

  //displaying sensor value on lcd
  lcd.clear();
  lcd.setCursor(0, 0); // setting cursor
  lcd.print("Water: ");//printing water level
  lcd.print(wlevel);
  
  lcd.setCursor(0, 1); // setting cursor 
  lcd.print("Temp: ");
  lcd.print(temp);
  lcd.print((char)223); // obtaining degree signnal
  lcd.print("C");

  // Checking the need of watering
  if (wlevel < threshold) {
    digitalWrite(wrelaypin, HIGH); // start watering
    lcd.setCursor(0, 0); // Updating LCD
    lcd.print("Watering...");
  } else {
    digitalWrite(wrelaypin, LOW);  // no watering 
  }

   // Checking the need of injecting npk fertilizer
  if (npklevel < threshold) {
    digitalWrite(npkrelaypin, HIGH); // start fertilizing
    lcd.setCursor(0, 1); // Updating lcd
    lcd.print("NPK Injecting...");
  } else {
    digitalWrite(npkrelaypin, LOW);  // no fertilizing
  }

  // Printing readings of sensor
  Serial.print("Water Level: ");
  Serial.print(wlevel);
  Serial.print(" | Temperature: ");
  Serial.print(temp);
  Serial.print(" C | NPK Level: ");
  Serial.println(npklevel);
}
