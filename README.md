# ESP8266-arduino-nano
How to connect an ESP8266 module to an arduino nano and using it as a wifi shield and how to create blynk apps
***********************************************************************************************



/*PER QUANTO RIGUARDA IL COLLEGAMENTO:

usare un abbassatore di tensione dai 5v di arduino a 3.3v 
Collegare il vcc dell'esp8266 ai 3.3v stabili
ch-pd a 3.3v stabili tramite una resistenza da 10k ohm
gnd al gnd DI ARDUINO(fondamentale)
TX dell'esp a D2 di arduino senza alcuna resistenza
RX dell'esp a D3 di arduino tramite una resistenza di 1k ohm verso arduino e una di pull out a gnd di 2k ohm

HOW TO CONNECT

Use a 5 to 3.3 voltage regulator to power the ESP module
connect esp8266's vcc to the stabilized 3.3v from the voltage regulator
connect ch-pd to 3.3v with a 10K ohm resistor
connect esp's gnd to arduino gnd(Fundamental)
connect esp's TX to arduino's D2 pin with no resistors
connect esp's RX to arduino's D3 pin with a 1k ohm resistor between arduino and esp and a 2k ogm pull out resistor to gnd */


*****************************************************************************************************************************


/* UTILIZZO 
se la scheda una volta connessa come riportato riporta garbage values con gli sketch di esempio:

USE
Use one of the sample sketches avaliable, if they turn garbage values on serial then: */


#include <SoftwareSerial.h>
SoftwareSerial ESPserial(2, 3); // RX | TX

void setup() 
{
  Serial.begin(9600);
  ESPserial.begin(115200);
  ESPserial.println("AT+UART_DEF=9600,8,1,0,0");
  delay(1000);
  ESPserial.end();
  // Start the software serial for communication with the ESP8266
  ESPserial.begin(9600);

  Serial.println("Ready");
  ESPserial.println("AT+GMR");
}

void loop() 
{
    //listen for communication from the ESP8266 and then write it to the serial monitor
    
    if ( ESPserial.available() )   {  Serial.write( ESPserial.read() );  }

    //listen for user input and send it to the ESP8266
    
    if ( Serial.available() )       {  ESPserial.write( Serial.read() );  }
}


/* Procedere poi a inserire: 
   Then use: */



#include <SoftwareSerial.h>
SoftwareSerial ESPserial(2, 3); // RX | TX

void setup() 
{
  Serial.begin(9600);

  ESPserial.begin(9600);

  Serial.println("Ready");
  ESPserial.println("AT+CWMODE=1 ");
}

void loop() 
{
    // listen for communication from the ESP8266 and then write it to the serial monitor
    if ( ESPserial.available() )   {  Serial.write( ESPserial.read() );  }

    // listen for user input and send it to the ESP8266
    if ( Serial.available() )       {  ESPserial.write( Serial.read() );  }
}


/*Then use: */



#include <SoftwareSerial.h>
SoftwareSerial ESPserial(2, 3); // RX | TX

void setup() 
{
  Serial.begin(9600);

  ESPserial.begin(9600);

  Serial.println("Ready");
  ESPserial.println("ATE0");
}

void loop() 
{
    // listen for communication from the ESP8266 and then write it to the serial monitor
    if ( ESPserial.available() )   {  Serial.write( ESPserial.read() );  }

    // listen for user input and send it to the ESP8266
    if ( Serial.available() )       {  ESPserial.write( Serial.read() );  }
}


****************************************************************************************************************************


/* PER CONNETTERE AL WIFI: 
   HOW TO CONNECT TO WIFI: */



/**
 * Based on these examples:
 * https://create.arduino.cc/projecthub/jeffpar0721/add-wifi-to-arduino-uno-663b9e
 * http://yaab-arduino.blogspot.nl/p/wifiesp.html
 * 
 * This sketch uses the library WiFiEsp. If it is not installed yet, install it via Tools > Manage Libraries
 * or get it from https://github.com/bportaluri/WiFiEsp and extract the Zip-file to Documents\Arduino\libraries\WiFiEsp
 * 
 * Check the output in the Serial Monitor.
 */

#include "WiFiEsp.h"

// Emulate EspSerial on pins 2/3 if not present
#ifndef HAVE_HWEspSerial
#include "SoftwareSerial.h"
SoftwareSerial EspSerial(2, 3); // RX, TX
#endif

char ssid[] = "SSID";           // your network SSID (name)
char pass[] = "pass";        // your network password (change it)
int status = WL_IDLE_STATUS;     // the Wifi radio's status

void setup() {
  // initialize serial for debugging
  Serial.begin(9600);
  // initialize serial for ESP module
  EspSerial.begin(9600);
  // initialize ESP module
  WiFi.init(&EspSerial);

  // check for the presence of the shield
  if (WiFi.status() == WL_NO_SHIELD) {
    Serial.println("WiFi shield not present");
    // don't continue
    while (true);
  }

  // attempt to connect to WiFi network
  while ( status != WL_CONNECTED) {
    Serial.print("Attempting to connect to WPA SSID: ");
    Serial.println(ssid);
    // Connect to WPA/WPA2 network
    status = WiFi.begin(ssid, pass);
  }

  // you're connected now, so print out the data
  Serial.println("You're connected to the network");
}

void loop()
{
  // check the network connection once every 10 seconds
  Serial.println();
  printCurrentNet();
  printWifiData();
  
  delay(10000);
}

void printWifiData() {
  // print your WiFi shield's IP address
  IPAddress ip = WiFi.localIP();
  Serial.print("IP Address: ");
  Serial.println(ip);

  // print your MAC address
  byte mac[6];
  WiFi.macAddress(mac);
  char buf[20];
  sprintf(buf, "%02X:%02X:%02X:%02X:%02X:%02X", mac[5], mac[4], mac[3], mac[2], mac[1], mac[0]);
  Serial.print("MAC address: ");
  Serial.println(buf);
}

void printCurrentNet() {
  // print the SSID of the network you're attached to
  Serial.print("SSID: ");
  Serial.println(WiFi.SSID());

  // print the MAC address of the router you're attached to
  byte bssid[6];
  WiFi.BSSID(bssid);
  char buf[20];
  sprintf(buf, "%02X:%02X:%02X:%02X:%02X:%02X", bssid[5], bssid[4], bssid[3], bssid[2], bssid[1], bssid[0]);
  Serial.print("BSSID: ");
  Serial.println(buf);

  // print the received signal strength
  long rssi = WiFi.RSSI();
  Serial.print("Signal strength (RSSI): ");
  Serial.println(rssi);
}


********************************************************************************************************************


/* CONNESSIONE A BLYNK 
   HOW TO CONNECT TO BLYNK */



/*************************************************************
  Download latest Blynk library here:
    https://github.com/blynkkk/blynk-library/releases/latest

  Blynk is a platform with iOS and Android apps to control
  Arduino, Raspberry Pi and the likes over the Internet.
  You can easily build graphic interfaces for all your
  projects by simply dragging and dropping widgets.

    Downloads, docs, tutorials: http://www.blynk.cc
    Sketch generator:           http://examples.blynk.cc
    Blynk community:            http://community.blynk.cc
    Follow us:                  http://www.fb.com/blynkapp
                                http://twitter.com/blynk_app

  Blynk library is licensed under MIT license
  This example code is in public domain.

 *************************************************************

  This example shows how to use ESP8266 Shield (with AT commands)
  to connect your project to Blynk.

  WARNING!
    It's very tricky to get it working. Please read this article:
    http://help.blynk.cc/hardware-and-libraries/arduino/esp8266-with-at-firmware

  Change WiFi ssid, pass, and Blynk auth token to run :)
  Feel free to apply it to any other example. It's simple!
 *************************************************************/

/* Comment this out to disable prints and save space */
#define BLYNK_PRINT Serial


#include <ESP8266_Lib.h>
#include <BlynkSimpleShieldEsp8266.h>

// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "ghJL_y2P5xHqf45es-9zuXYbey3BLR50";

// Your WiFi credentials.
// Set password to "" for open networks.
char ssid[] = "SSID";
char pass[] = "Pass";



// or Software Serial on Uno, Nano...
#include <SoftwareSerial.h>
SoftwareSerial EspSerial(2, 3); // RX, TX

// Your ESP8266 baud rate:
#define ESP8266_BAUD 9600

ESP8266 wifi(&EspSerial);

void setup()
{
  // Debug console
  Serial.begin(9600);

  delay(10);

  // Set ESP8266 baud rate
  EspSerial.begin(ESP8266_BAUD);
  delay(10);

  Blynk.begin(auth, wifi, ssid, pass);
}

void loop()
{
  Blynk.run();
}

/* DOPODICHE' DALL'APP BLYNK SU CELLULARE INSERIRE UN BOTTONE CHE CONTROLLA IL LED SUL PIN D5 COME DA CIRCUITO */
/*THEN OPEN THE BLYNK APP ON YOUR SMARTPHONE AND CREATE A BUTTON WHICH CONTROLS D5 PIN'S LED*/
