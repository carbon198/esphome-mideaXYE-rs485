# esphome-mideaXYE-rs485
ESPhome files to communicate with Midea AC units via RS485 XYE (CCM) terminals.

This project will allow you to wirelessly communicate with your Midea AC unit via the XYE terminals.  These terminals are normally used for communication with a Central Controller Module (CCM) but the data has been reverse engineered (https://codeberg.org/xye/xye).  I initially used the code from bunicutz project https://github.com/Bunicutz/ESP32_Midea_RS485/tree/main, but as of ESPHome 2025.2.0 custom components are no longer supported and that code does not work.  This project doesn't use custom components, just several lambda functions and a header file.  I'm not a C++ developer, so forgive me if the code isn't very elegant. It gets the job done though!

# Hardware
ESPHome device, RS-485 transceiver with automatic flow control (if not you will need to edit my code to switch flow control manually after serial read/write), Midea-like heat pump or air handler with XYE terminals.  Connect ESP Vin, GND, GPIO16 to RX, and GPIO17 to TX on the UART side of the transceiver (can edit these in the header file if necessary).  On the RS-485 connect A to X, B to Y, and GND to E (optional).

# Software
This was intended for use with Home Assistant, the YAML file is for ESPHome. 
1. Create a new ESPHome device and configure it with your wifi info
2. Paste the contents of "midea-xye.yaml" under the auto-populated info in your new ESPHome device.
3. Copy "xyeVars.h" into your /config/esphome directory (or wherever your ESPhome YAML files are in your setup).
4. Edit the #define section of the xyeVars.h file to match your GPIO pins for RX and TX.

# Home Assistant
The device that is created will have the following entities:
Select Fan Mode - these are standard Home Assistant fan modes, compatible with a climate device
Select HVAC Mode - these are standard HVAC modes, compatible with a climate device
Input Number Set temperature - this is the current target temperature and input for target temperature

Sensor Inlet Air Temperature - T1, air measured coming into the air handler. If you have "follow me" turned on this sensor will eventually get stale.\
Sensor Coil A Temp - Refrigerant temperature\
Sensor Coil B Temp - Refrigerant temperature\
Sensor Outdoor Temp\
Sensor Error Codes - 2 bytes of error codes. I haven't had an error code to test these but they will be the integer form of the 2 hex codes. The reverese engineered XYE notes aren't very clear on these.\
Sensor Full data string - Integer list of all the data received most recently from the Midea unit. Can match these up to the XYE reverse engineered values to check what data is coming in. Sorry it's not in hex, my C++ isn't strong enough haha.

# Background
I have a Carrier heatpump (38MARB) and ducted air handler (40MBAB), and the heat pump is a rebranded Midea unit.  The included controls are fine, but I wanted to add wireless control and integrate with Home Assistant without using a 24V thermostat.  I can't find anything definitive but I suspect using a 24V thermostat would prevent any benefits from the variable frequency drive of the heat pump (the unit won't know the setpoint so it won't know how to modulate).  

There are already ESPHome components for communication with Midea heat pumps via H+/- terminals and IR, but my controller is HA/HB which is not compatible with the hardwired code for H+/- and I wanted 2-way communication of data which isn't possible with the IR component.  I found bunicutz's code for XYE terminals and discovered my air handler had these terminals, so I tried it out and it worked! Unfortunately, it relies on custom components so I had to rewrite it without them using lambda functions. 
