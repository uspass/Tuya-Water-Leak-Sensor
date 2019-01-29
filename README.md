# Tuya-Water-Leak-Sensor
Tuya Water Leak Sensor 8266ex based

Basically the sensor works like this:
The EFM micro-controller (U3) is powered by the battery through Q2 
which acts like a diode (with a low drop-off voltage) protecting it from reverse polarity.
This micro-controller takes care of:
  a. water leak tips reading p1.0
  b. battery low reading p1.2. It looks like it reads 1/2 voltage and when that voltage drops under port
    voltage threshold it reads 0, otherwise it reads 1.
  c. push button K1 reading p0.2. For WiFi pairing.
  d. Buzzer B2 writing p0.6. Sound alarm.
  e. LED D1 writing p0.7. WiFi and visual alarm.
  f. Reset ESP (EXT_RSTB) writing p1.5.
  g. ESP power writing p1.7. Power control to save battery.
  h. GPIO5 linked to p0.0.
  In order to save power, ESP (U1) is powered through Q3 which is normally closed (VM0=0).

The event sequence is as follows:
When an event appears, water over the terminals (or I suspect battery low, never tried that), 
EFM powers on ESP by opening Q3. 
Probably sends also a reset (f).
ESP gets somehow the event type (water leak or battery low), 
connects to WiFi, establish the communication with the cloud and sends info to the cloud. 
It's unclear if it uses GPIO5 to read the event type or to send something like task done.
Anyway, after that, EFM cuts down ESP power and looks for the next event.
All that takes like 3-5 seconds.
