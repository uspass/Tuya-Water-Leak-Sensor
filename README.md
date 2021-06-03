# Tuya Water Leak Sensor SKU-S2502 Converted to Tasmota
MCU Product ID: {"P":"SmFKLTOcGHbPQvmh","v":"1.1.0"} 
Wifi 8266ex 

![20190119_102556x](https://user-images.githubusercontent.com/42880649/120639804-6dac6180-c47a-11eb-85a8-bf8dbfecacb2.jpg)

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

When an event shows, water over the terminals, MCU powers on ESP by opening Q3.

ESP gets the event type (water leak and battery status) using Tuya protocol, connects to WiFi, establish the communication with the cloud and sends info to the cloud. 

After that, MCU cuts down ESP power and looks for the next event.

All that takes like 3-5 seconds.

Use Tx Rx GND VMO(+3V) and GPIO0 to GND to flash Tasmota.

From now on, every single change in Tasmota should be done by quickly, with your fingers wet, touch the water leak tips.

I assume you have worked before with Tasmota.

#### Set the wifi credentials.

#### Set the Mqtt config.

The following programming sequence is:

#### Module 54

#### Backlog SetOption1 1; SetOption65 1; SetOption66 0; SetOption36 0; SetOption19 1; SwitchMode 1

#### TuyaMCU 51,21

#### Backlog DeviceName Water_Leak; FriendlyName1 Water_Leak


Tuya CmndData:

65010001006604000101

That translates to:

First 10 (Rule1): 

6501000101 ON (Wet)

6501000100 OFF (Dry)

Last 10 (Rule2):

6604000101 Battery High

6604000102 Battery Medium

6604000103 Battery Low


#### Rule1 ON TuyaReceived#Data$<55AA0005000A6501000101 DO publish2 stat/%topic%/STATUS ON ENDON ON TuyaReceived#Data$<55AA0005000A6501000100 DO publish2 stat/%topic%/STATUS OFF ENDON

#### Rule2 ON TuyaReceived#Data$|6604000101 DO publish2 stat/%topic%/BATT High ENDON ON TuyaReceived#Data$|6604000102 DO publish2 stat/%topic%/BATT Medium ENDON ON TuyaReceived#Data$|6604000103 DO publish2 stat/%topic%/BATT Low ENDON


The next rule creates a device in Home Assistant:

#### Rule3 ON system#boot DO publish2 homeassistant/binary_sensor/%macaddr%_moisture/config {"name":"Water Leak","unique_id":"%topic%_%macaddr%","device_class":"moisture","device":{"identifiers":["%macaddr%"],"name":"Water Leak","manufacturer":"Tasmota","model":"SKU-S2502"},"state_topic":"%topic%/stat/STATUS"} ENDON ON system#boot DO publish2 homeassistant/sensor/%macaddr%_battery/config {"name":"Water Leak Battery","unique_id":"%topic%_Battery_%macaddr%","icon":"hass:battery","device":{"identifiers":["%macaddr%"],"name":"Water Leak","manufacturer":"Tasmota","model":"SKU-S2502"},"state_topic":"%topic%/stat/BATT"} ENDON

#### Backlog Rule1 1; Rule2 1; Rule3 1


