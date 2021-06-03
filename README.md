# Tuya-Water-Leak-Sensor
Tuya Water Leak Sensor 8266ex based

Basically the sensor works like this:
The MCU (U3) is powered by the battery through Q2 which acts like a diode (with a low drop-off voltage) protecting it from reverse polarity.
This micro-controller takes care of:
  1. water leak tips reading p1.0
  1. battery low reading p1.2. 
  1. push button K1 reading p0.2. For WiFi pairing.
  1. Buzzer B2 writing p0.6. Sound alarm.
  1. LED D1 writing p0.7. WiFi and visual alarm.
  1. Reset ESP (EXT_RSTB) writing p1.5.
  1. ESP power writing p1.7. Power control to save battery.
  1. GPIO5 linked to p0.0.
  In order to save power, ESP (U1) is powered through Q3 which is normally closed (VM0=0).

The event sequence is as follows:
When an event appears, water over the terminals (or I suspect battery low, never tried that), 
MCU powers on ESP by opening Q3. 
ESP gets the event type (water leak or battery status), 
connects to WiFi, establish the communication with the cloud and sends info to the cloud. 
After that, MCU cuts down ESP power and looks for the next event.
All that takes like 3-5 seconds.


