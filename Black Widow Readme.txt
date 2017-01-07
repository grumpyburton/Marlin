Tevo Black Widow Firmware - Marlin 1.1.0-RC8 by dot_bob
Release Notes

Background:
In short this port is used for both versions of the Tevo Black Widow that ship with and without the BLTOUCH sensor.

Having a Tevo Black Widow with the BLTOUCH sensor I often switch between the current community firmwares with and without 
BLTOUCH support.  I was getting tired of the performance differences between the two so I decided to create my own port 
that can be easily switched between the two different code bases.  Being an embedded linux kernel developer myself 
I decided to make my own port of the Marlin firmware.  My goal was to make the firmware as vanilla as possible allowing
future ports to mainline to be quick and simple.  For the desciription of changes see below.

Usage:

To configure the firmware to compile in BLTOUCH support uncomment the following line located at the top of the file "Configuration.h"
by removing the "//" characters from beggining line:

//#define TEVO_BLTOUCH

The firmware is setup to use the BLTOUCH mount from ANTCLABS the maker of the BLTouch.  Their mount can be
found here: http://www.thingiverse.com/thing:1761761

Note if you are using the BLTOUCH "#define ENABLE_LEVELING_FADE_HEIGHT" is enabled in Configuration.h.  This is used by issuing 
the GCODE command M420 Z<height>.  If you are having vertical lines in your print due to mesh leveling correction this will help 
combat this.  I have been experementing with using "M420 Z2" for a 2mm fade height as my bed if fairly level.

You may want to configure your specific machine printing limits by changing the following lines in the "Configuration.h":

#define min_software_endstops false // If true, axis won't move to coordinates less than HOME_POS.
#define max_software_endstops true // If true, axis won't move to coordinates greater than the defined lengths below.

// @section machine

// Travel limits after homing (units are in mm)
#define X_MIN_POS 0
#define Y_MIN_POS 0
#define Z_MIN_POS 0
#define X_MAX_POS 355
#define Y_MAX_POS 250
#define Z_MAX_POS 250

Features:
- Simple to add and remove BLTouch support.
- Support extruder cooling on header HE1 (Heater for extruder #1).
- Extruder fan is set to switch on above 50 DegC
- Extruder fan speed to 80% to quiet down the system while printing.
- Leveling fade support is added (can improve print quality for BLTouch).
- Quick access to the Babystep Z option.

GCode scripts:

; Tevo Black Widow Start Script
M280 P0 S160 ; release BLTouch alarm (OK to send for Non BLTouch)
M420 Z2 ; set fade leveling at 2mm for BLTouch (OK to send for Non BLTouch)
G28 ; home all
G29 ; probe bed
G92 E0 ;zero the extruded length
; move head out of bounds
G1 X0.0 Y148.0 Z10.0 F2000
G1 X-12.0 F2000
; perform wipe and prime
G1 Z0.0 F1000
G1 X3.0 F1000
G1 Z0.2 Y60.0 E9.0 F1000.0 ; prime
G1 Y100.0 E12.5 F1000.0 ; prime
G92 E0 ; zero extruder again
M117 Printing...

; Tevo Black Widow Stop Script
M104 S0 ; turn off extruder
M140 S0 ; turn off bed
G28 X0 ; home X axis
G1 Y245 ; move Y axis to end position
M84 ; disable motors
M107 ; turn off fan

Changes:
Version 1.1.0-RC8 12-27-16 Version 2
- Added "Babystep Z" to the Main Menu when performing a print.  This allows the user to quickly access the the realtime z height ajustment.
- Changed BABYSTEP_MULTIPLICATOR to 10 as the default of 1 is way to slow to make noticeable corrections.
- Lowered LCD_UPDATE_THRESHOLD from 135 to 50 to improve display response time.
- Updated the above gcode start script to a version that is more friendly with corner clamps.

Version 1.1.0-RC8 12-22-16 (Initial Release) Version 1
- Removed Tevo boot screen to speed up the marlin boot process.
- Added "#define TEVO_BLTOUCH" to the Configuration.h and associated #ifdef to probe support sections to enable BLTOUCH support.
- Added support to drive the extruder cooling fan on port E1 of the MKS Gen motherboard.  
- Changed extruder fan speed to 80% to reduce noise and vibration, see the file "Configuration_adv.h" at the
line "#define EXTRUDER_AUTO_FAN_SPEED   204 // == 80% to reduce noise"
- Increased the display menu timeout from 15 seconds to two minutes by changing the line "#define LCD_TIMEOUT_TO_STATUS 15000"
to "#define LCD_TIMEOUT_TO_STATUS 120000" in the file ultralcd.h.
- Used a combination of settings derived from the offical Tevo port of the Black Widow firmware and currently released community
firmware releases.


