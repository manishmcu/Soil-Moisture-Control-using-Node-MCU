# Soil-Moisture-Control-using-Node-MCU
Soil Moisture sensing using Node MCU, monitoring and pump on off automatically.

### Coding Part:

```
#define BLYNK_PRINT Serial

#include <SoftwareSerial.h>
SoftwareSerial DebugSerial(2, 3); // RX, TX
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include "DHT.h"           // including the library of DHT11 temperature and humidity sensor
#include <SimpleTimer.h>   //including the library of SimpleTimer
#define DHTTYPE DHT11      // DHT 11

#define dht_dpin 14
DHT dht(dht_dpin, DHTTYPE);
SimpleTimer timer;
char auth[] = "76a7e824d6e04658bedb3d9c34874505";            // You should get Auth Token in the Blynk App.
                                           // Go to the Project Settings (nut icon).

char ssid[] = "AndroidAP";    // Your WiFi credentials.
char pass[] = "fptb1505";  // Set password to "" for open networks.
float t;                                   // Declare the variables
float h;
int motor=D1;


void setup()
{
    Serial.begin(9600);// Debug console
    Blynk.begin(auth,ssid,pass);
    dht.begin();
    timer.setInterval(2000, sendUptime);
    timer.setInterval(3000L, blinkLedWidget);
    pinMode(D1,OUTPUT);
}

void sendUptime()
{
 
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  Serial.println("Humidity and temperature\n\n");
  Serial.print("Current humidity = ");
  Serial.print(h);
  Serial.print("%  ");
  Serial.print("temperature = ");
  Serial.print(t);
  Blynk.virtualWrite(V6, t);
  Blynk.virtualWrite(V5, h);
 
}


int Sensorvalue=0;
int sensor_pin=A0;
int moisture_percentage=0;




void blinkLedWidget()
{

// Sensorvalue = map(Sensorvalue, 0,1023,1023,0);
//sensorValuePercent = (300/1023) * 100; // In Prozent﻿
moisture_percentage = ( 100.00 - ( (analogRead(sensor_pin)/1023.00) * 100.00 ) );
Serial.print("Soil Moisture(in Percentage) = ");
Serial.print(moisture_percentage);
Serial.println("%");

Blynk.virtualWrite(V4, moisture_percentage);
  }
void loop()    
{
  Blynk.run();
  timer.run();
  if(moisture_percentage<60)
{digitalWrite(D1,HIGH);}
if(moisture_percentage>70)
{digitalWrite(D1,LOW);}
 
}
```
