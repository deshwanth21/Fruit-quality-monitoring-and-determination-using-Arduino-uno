# Fruit-quality-monitoring-and-determination-using-Arduino-uno
#include "ESP8266WiFi.h"
#include <DHT.h>
#include <LiquidCrystal.h> // Include the LCD library

#define DHTPIN 7     // What digital pin we're connected to
#define DHTTYPE DHT11   // DHT 11
const int mq4_pin = A1;
const int buzz = 4;
DHT dht(DHTPIN, DHTTYPE);
LiquidCrystal lcd(12, 11, 5, 4, 3, 2); // Initialize the LCD module

long _Start;
int ret_val;
long duration, Gas_level, gas_level; // Temporary variables

void setup()
{
  Serial.begin(9600);
  pinMode(mq4_pin, INPUT);
  pinMode(buzz, OUTPUT);
  dht.begin();
  ini_iot();
  _Start = millis();

  lcd.begin(16, 2); // Initialize the LCD module with the desired number of columns and rows
  lcd.print("Hello, LCD!"); // Display a message on the LCD
}

void loop()
{
  int c = digitalRead(Sound_Pin);
  Serial.println("Sound_level:" + String(c));

  int a = map((int)analogRead(mq4_pin), 0, 1023, 0, 100);
  Serial.println("mq4_level:" + String(a));

  if (a > 50)
  {
    digitalWrite(buzz, HIGH);
    delay(1000);
    digitalWrite(buzz, LOW);
    delay(1000);
  }
  else
  {
    digitalWrite(buzz, LOW);
  }

  Gas_level = map((int)analogRead(Gas_pin), 0, 1023, 0, 100); // Reading data from the soil moisture sensor
  Serial.println("Gas_level:" + String(Gas_level));

  if (Gas_level > 20) // If it is high
  {
    digitalWrite(Buzz_Pin1, HIGH);
    delay(500);
  }
  else
  {
    digitalWrite(Buzz_Pin1, LOW);
    delay(500);
  }

  int h = dht.readHumidity();
  int t = dht.readTemperature();
  Serial.println("Temperature=" + String(t));
  Serial.println("Humidity=" + String(h));

  if (t >= 32)
  {
    digitalWrite(buzz, HIGH);
    delay(500);
  }
  else
  {
    digitalWrite(buzz, LOW);
    delay(500);
  }

  Serial.println("-----------------------");
  delay(2000);

  // Display temperature, humidity, and mq4 level on the LCD
  lcd.clear(); // Clear the LCD screen
  lcd.setCursor(0, 0); // Set the cursor to the first column of the first row
  lcd.print("Temp: " + String(t) + "C");

  lcd.setCursor(0, 1); // Set the cursor to the first column of the second row
  lcd.print("Humidity: " + String(h) + "%");

  String getData2 = "&field1=" + String(t) + "&field2=" + String(h) + "&field3=" + String(a);

  if (millis() - _Start > 30000)
  {
    senddata(getData2);
    Serial.println("entered");
    _Start
