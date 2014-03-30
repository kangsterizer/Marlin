Prusa i3 8" Makerfarm fw
------------------------

Changes
~~~~~~~
- adjusted Marlin's values for Prusa i3 8" from Markerfarm (RAMPS board with small LCD)
- enabled storage of settings in EEPROM

Additional software
~~~~~~~~~~~~~~~~~~~
- Slic3r (STL to gCode)
- Pronterface (control printer)
- Arduino (compile+upload fw)

How to calibrate
~~~~~~~~~~~~~~~~

This step is very important. You cannot get a decent print without calibration.

- Home all axises. Calibrate endpoints manually by un/screwing screws so that the extruder never goes outside your bed plate.
- Re home axises until all endpoints are good enough.

- put a ruler on the bed plate along the X axis and start at one edge of the extruder.
- connect via pronterface and check was current values are. These are in the text/console box when you connect and look like "M92 X80.00 Y80.00 Z4000.00 E841"
- move extruder by 100mm, verify on the ruler
- via pronterface again, change the value for X in the M92 command (so type something like "M92 X81.00 Y80.00 Z4000.00 E841")
- value goes higher if it didn't move far enough, else lower. There are calculators, but it's easy/easier to just do some try & measure until you get a good measurement.
- repeat for Y axis.
- repeat for Z axis (you don't need to put the ruler at the hot head extruder, take any reference point on the whole head, it's easier).
- finally, move Z up a bit, enough to extrude filament without messing things up, then measure 100mm of filament right above the hot head. Mark the 100mm with a sharpie.
- extrude 100mm at reasonable speed (I use 30mm/min) and measure the difference between your sharpie mark and the hot head. Modify E consequently.
- warning: don't modify by much more than 1 or 2 at a time, or you'll mess up your filament feeding, E changes quite fast.
- once done/happy, enter 'M500' to store values (else, they're lost at power off).

Here are my calibrated values, for example: 'M92 X82.00 Y80.70 Z4000.00 E846.50'

How to clean up the extruder before the print start/etc.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In Slic3r's printer settings, add or replace custom start g-code:

.. code::

   M140 S0          ;set bed temp to 0 (set this higher if you use bed temp)
   M104 S185        ;set head temp to 185 (likewise)
   G28              ;home all axis - this is necessary to get reference position x=0 y=0 z=0
   M83              ;set printer to relative coordinates mode
   M109 S185        ;set head temp & wait until it is reached
   G1 X5 F500       ;move to X=5, speed 500
   G1 Y80 E10 F500  ;move to Y=80 while extruding E=10 of filament, speed 500

This effectively will make a 80mm trace of filament on the side of your bed plate before starting the print, which generally is good enough to clean up the extruder.

End g-code:

.. code::

   M104 S0          ;turn off head temperature
   M140 S0          ;turn off bed temp
   G1 F30 E-1       ;stop extrude (retracts filament by 1 at speed 30)
   G28 X0           ;park X axis first
   G28 Y0           ;park Y axis
   G1 Z30 F5000     ;lift or lower head (it'll make it easy to clean if needed, and low enough so that next print doesn't need to lower the head for too long)
   M84              ;disable motors
