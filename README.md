//arduino pins to which NEO-6M GPS module is connected and variable that stores default GPS baud rate.


#include <TinyGPS++.h>
#include <SoftwareSerial.h>

int RXPin = 2;
int TXPin = 3;
int GPSBaud = 9600;

TinyGPSPlus gps;
SoftwareSerial gpsSerial(RXPin, TXPin)
to initiate the serial communication with the PC as well as the GPS module
void setup()
{
  Serial.begin(9600);
  gpsSerial.begin(GPSBaud);
}



//In loop function, we call displayInfo() custom function which prints location information(Latitude, Longitude & Altitude) and UTC(Date & Time) on the serial monitor, every time a new NMEA sentence is correctly encoded


void loop()
{
  while (gpsSerial.available() > 0)
    if (gps.encode(gpsSerial.read()))
      displayInfo();

  if (millis() > 5000 && gps.charsProcessed() < 10)
  {
    Serial.println(F("No GPS detected"));
    while(true);
  }
}


//to use Arduino as USB to TTL Converter.
// Choose two Arduino pins to use for software serial


int RXPin = 2;
int TXPin = 3;

//Default baud of NEO-6M is 9600
int GPSBaud = 9600;

// Create a software serial port called "gpsSerial"


SoftwareSerial gpsSerial(RXPin, TXPin);

void setup()
{
  
  
  // Start the Arduino hardware serial port at 9600 baud
  Serial.begin(9600);

  // Start the software serial port at the GPS's default baud
  gpsSerial.begin(GPSBaud);
}

void loop()
{
  // Displays information when new sentence is available.
  while (gpsSerial.available() > 0)
    Serial.write(gpsSerial.read());
}



//test sketch will print the location information(Latitude, Longitude & Altitude) and UTC(Date & Time) on the serial monitor.


#include <TinyGPS++.h>
#include <SoftwareSerial.h>

// Choose two Arduino pins to use for software serial


int RXPin = 2;
int TXPin = 3;

int GPSBaud = 9600;

// Create a TinyGPS++ object

TinyGPSPlus gps;

// Create a software serial port called "gpsSerial"

SoftwareSerial gpsSerial(RXPin, TXPin);

void setup()
{
  // Start the Arduino hardware serial port at 9600 baud
  Serial.begin(9600);

  // Start the software serial port at the GPS's default baud
  gpsSerial.begin(GPSBaud);
}




void loop()
{
  // This sketch displays information every time a new sentence is correctly encoded.
  while (gpsSerial.available() > 0)
    if (gps.encode(gpsSerial.read()))
      displayInfo();

  // If 5000 milliseconds pass and there are no characters coming in
  // over the software serial port, show a "No GPS detected" error
  if (millis() > 5000 && gps.charsProcessed() < 10)
  {
    Serial.println("No GPS detected");
    while(true);
  }
}



void displayInfo()
{
  if (gps.location.isValid())
  {
    Serial.print("Latitude: ");
    Serial.println(gps.location.lat(), 6);
    Serial.print("Longitude: ");
    Serial.println(gps.location.lng(), 6);
    Serial.print("Altitude: ");
    Serial.println(gps.altitude.meters());
  }
  else
  {
    Serial.println("Location: Not Available");
  }
  
  Serial.print("Date: ");
  if (gps.date.isValid())
  {
    Serial.print(gps.date.month());
    Serial.print("/");
    Serial.print(gps.date.day());
    Serial.print("/");
    Serial.println(gps.date.year());
  }
  else
  {
    Serial.println("Not Available");
  }

  Serial.print("Time: ");
  if (gps.time.isValid())
  {
    if (gps.time.hour() < 10) Serial.print(F("0"));
    Serial.print(gps.time.hour());
    Serial.print(":");
    if (gps.time.minute() < 10) Serial.print(F("0"));
    Serial.print(gps.time.minute());
    Serial.print(":");
    if (gps.time.second() < 10) Serial.print(F("0"));
    Serial.print(gps.time.second());
    Serial.print(".");
    if (gps.time.centisecond() < 10) Serial.print(F("0"));
    Serial.println(gps.time.centisecond());
  }
  else
  {
    Serial.println("Not Available");
  }

  Serial.println();
  Serial.println();
  delay(1000);
}



#include <SPI.h>
#include <LoRa.h>
#include <TinyGPS++.h>
#include <SoftwareSerial.h>

// Choose two Arduino pins to use for software serial
int RXPin = 3;
int TXPin = 4;   

// Create a TinyGPS++ object
TinyGPSPlus gps;

SoftwareSerial gpsSerial(RXPin, TXPin);

void setup() {
  Serial.begin(9600);
   gpsSerial.begin(9600);
  while (!Serial);

  Serial.println("LoRa Sender");

  if (!LoRa.begin(433E6)) {
    Serial.println("Starting LoRa failed!");
    while (1);
  }

  LoRa.setTxPower(20);
  
}




void loop() {
  
 while (gpsSerial.available() > 0)
    if (gps.encode(gpsSerial.read()))
    
        if (gps.location.isValid())
  {
    Serial.println("Sending to LoRa");
    LoRa.beginPacket();
    LoRa.print("Lat: ");
    LoRa.print(gps.location.lat(), 6);
    LoRa.print("c");
    LoRa.print("Long: ");
    LoRa.print(gps.location.lng(), 6);
    Serial.println("Sent via LoRa");
    LoRa.endPacket();
  }

}


//lora receiver code
 /*Program to receive the value of temperature and Humidity via LoRa and prin on LCd
 */



#include <SPI.h> //SPI Library 
#include <LoRa.h> //LoRa Library 
#include <LiquidCrystal.h> //Library for LCD

const int rs = 8, en = 7, d4 = 6, d5 = 5, d6 = 4, d7 = 3; //Mention the pin number for LCD connection
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);//Initialize LCD method

void setup() {
  Serial.begin(9600); //Serial for Debugging 
  
  lcd.begin(16, 2); //Initialise 16*2 LCD
  lcd.print("Arduino LoRa"); //Intro Message line 1
  lcd.setCursor(0, 1);
  lcd.print("Receiver"); //Intro Message line 2
  delay(2000);
  
  if (!LoRa.begin(433E6)) { //Operate on 433MHz
    Serial.println("Starting LoRa failed!");
    lcd…
