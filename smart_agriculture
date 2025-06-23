#include <Servo.h>
#include <LiquidCrystal_I2C.h>

// Pin Definitions
const int trigPin = 2;
const int echoPin = 4;
const int buzzerPin = 8;
const int pumpPin = 12;
const int servoPin = 11;

const int tempSensorPin = A0;
const int moistureSensorPin = A1;
const int humiditySensorPin = A2;

// Objects
Servo vents;
LiquidCrystal_I2C lcd(0x20, 16, 2);  // Adjust address if needed

void setup() {
  Serial.begin(9600);

  // Initialize I/O pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(pumpPin, OUTPUT);
  
  // Attach servo motor
  vents.attach(servoPin);

  // Initialize LCD
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Initializing...");
  delay(1000);
}

void loop() {
  // Read temperature
  int tempValue = analogRead(tempSensorPin);
  float voltage = (tempValue / 1023.0) * 5000.0;
  float temperature = (voltage - 500) * 0.1;
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" C");

  // Read soil moisture
  int moistureValue = analogRead(moistureSensorPin);
  float moisturePercent = (moistureValue / 876.03) * 100.0;
  Serial.print("Soil Moisture: ");
  Serial.print(moisturePercent);
  Serial.println(" %");

  // Pump control
  if (moisturePercent <= 50.0 && temperature >= 35.0) {
    digitalWrite(pumpPin, HIGH);
  } else {
    digitalWrite(pumpPin, LOW);
  }

  // Read humidity
  int humidityRaw = analogRead(humiditySensorPin);
  int humidityPercent = map(humidityRaw, 0, 1023, 0, 100);
  Serial.print("Humidity: ");
  Serial.print(humidityPercent);
  Serial.println(" %");

  // Ultrasonic sensor distance
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  long duration = pulseIn(echoPin, HIGH);
  float distance = (duration * 0.034) / 2.0;

  // Buzzer control based on distance
  if (distance <= 40.0) {
    digitalWrite(buzzerPin, HIGH);
  } else {
    digitalWrite(buzzerPin, LOW);
  }

  // Servo control based on humidity
  if (humidityPercent <= 50) {
    for (int pos = 0; pos <= 90; pos++) {
      vents.write(pos);
      delay(20);
    }
  } else {
    for (int pos = 90; pos >= 0; pos--) {
      vents.write(pos);
      delay(20);
    }
  }

  // Display all values on LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("T:");
  lcd.print(temperature, 1);
  lcd.print("C M:");
  lcd.print(moisturePercent, 0);
  lcd.print("%");

  lcd.setCursor(0, 1);
  lcd.print("Humidity:");
  lcd.print(humidityPercent);
  lcd.print("%");

  delay(1000);
}
