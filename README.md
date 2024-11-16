#include <LiquidCrystal_I2C.h>
 
// Pin definitions
const int trigPin = 9;
const int echoPin = 10;
const int irSensorPin = 2;
 
// Create an LCD object
LiquidCrystal_I2C lcd(0x27, 20, 4); // Change address as needed
 
void setup() {
  Serial.begin(9600);
  lcd.begin();
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(irSensorPin, INPUT);
  lcd.print("Fuel Level Monitor");
}
 
void loop() {
  long duration, distance;
  
  // Trigger the ultrasonic sensor
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  // Read the echo pin
  duration = pulseIn(echoPin, HIGH);
  
  // Calculate the distance
  distance = (duration * 0.034 / 2); // Distance in cm
 
  // Convert distance to fuel level (assuming a specific tank height)
  int fuelLevel = 100 - (distance / 2); // Example calculation
  
  // Display on LCD
  lcd.setCursor(0, 1);
  lcd.print("Fuel Level: ");
  lcd.print(fuelLevel);
  lcd.print(" % ");
  
  // Check fuel level
  if (fuelLevel <= 10) {
    lcd.setCursor(0, 2);
    lcd.print("Status: Empty ");
  } else if (fuelLevel >= 90) {
    lcd.setCursor(0, 2);
    lcd.print("Status: Full  ");
  } else {
    lcd.setCursor(0, 2);
    lcd.print("Status: OK    ");
  }
 
  // Check IR sensor
  if (digitalRead(irSensorPin) == HIGH) {
    sendNotification(); // Function to send message via NodeMCU
  }
  
  delay(1000); // Update every second
}
 
void sendNotification() {
  // Code to send message via NodeMCU
}

5.3 NodeMCU Code
#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <UniversalTelegramBot.h>
 
// Wi-Fi credentials
const char* ssid = "Your_SSID";
const char* password = "Your_PASSWORD";
 
// Telegram bot token and chat ID
const char* botToken = "YOUR_BOT_TOKEN";
const char* chat_id = "YOUR_CHAT_ID";
UniversalTelegramBot bot(botToken, WiFiClient());
 
void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}
 
void loop() {
  // Check for messages to send (implement as needed)
}
 
void sendMessage(String message) {
  bot.sendMessage(chat_id, message, "");
}
