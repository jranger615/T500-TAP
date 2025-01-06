 <H1>T500 TAP Guide for Stock HOT END</h1> 
I am providing instructions for a 3D Printed OptTap V2.4 RC8 or the CHAOTICLAB Voron Tap V2.0. Below I will list the details of Each/

<h4>OptTap V2.4 RC8</h4>
https://github.com/VoronDesign/Voron-Tap/tree/main/OptoTap
<ul>
<li>Cheaper </li>
<li>More Assembly</li>
<li>You loose 20MM of Y Axis Room</li>
</ul>
<h4>CHAOTICLAB Voron Tap V2.0</h4>
<ul>
<li>Precise Engineering</li>
<li>Only 2 Parts to Print</li>
<li>Only Requires 4 m3 Heated Inserts</li>
<li>Loose 24MM of T Axis Room</li>
</ul>


<h2>Wiring the TAP Sensor</h2>
The T500 Stock 4 Pin Probe Connector is as Follows:
<ul>
</ul>
<li>Brown = 24v </li>
<li>Blue = Ground</li>
<li>Black = Signal </li> 

<h4>OptTap V2.4 RC8</h4>
Cut your Sensor Wire off or Order new 4 pin connectos
Stock Fytec Kit Connections
<ul>
</ul>
<li>Brown Wire > Red Wire </li>
<li>Blue Wire > Black Wire</li>
<li>Black Wire > Green Wire</li>

<h4>CHAOTICLAB Voron Tap V2.0</h4>
Cut your Sensor Wire off or Order new 4 pin connectos
<ul>
</ul>
<li>Brown Wire > Red Wire </li>
<li>Blue Wire > Black Wire</li>
<li>Black Wire > Yellow Wire</li>


<h2>Stepper_y changes</h2>
```
position_endstop: -30 #adjustment for New Offset of Y Axis oddly this brings the hotend to the end of the thermal sheet. You coudl set this to 50 to get to closer to the front of the bed
position_min: -30 #adjustment for New Offset of Y Axis, oddly this brings the hotend to the end of the thermal sheet. You coudl set this to 50 to get to closer to the front of the bed
position_max: 503
```
<h2>probe changes</h2>

<br />[probe]
<br />pin:^!PE8  #This was changed from Inital Sensors PE8. We invert the polarity (!) and set pull up (^)
<br />x_offset: 0 #Adjust to 0 as Nozzle is now the sensor
<br />y_offset: 0 #Adjust to 0 as Nozzle is now the sensor
<br />#z_offset: -1.42 #Comment Out and Redo your Z_offset in calibration
<br />speed: 10.0
<br />samples: 3
<br />samples_result: median
<br />sample_retract_dist: 3.0
<br />samples_tolerance: 0.006
<br />samples_tolerance_retries: 10
<br />activate_gcode:
<br />    {% set PROBE_TEMP = 275 %}
<br />    {% set MAX_TEMP = PROBE_TEMP + 5 %}
 <br />   {% set ACTUAL_TEMP = printer.extruder.temperature %}
<br />    {% set TARGET_TEMP = printer.extruder.target %}
<br />
<br />    {% if TARGET_TEMP > PROBE_TEMP %}
 <br />       { action_respond_info('Extruder temperature target of %.1fC is too high, lowering to %.1fC' % (TARGET_TEMP, PROBE_TEMP)) }
 <br />       M109 S{ PROBE_TEMP }
<br />    {% else %}
 <br />       # Temperature target is already low enough, but nozzle may still be too hot.
 <br />       {% if ACTUAL_TEMP > MAX_TEMP %}
 <br />           { action_respond_info('Extruder temperature %.1fC is still too high, waiting until below %.1fC' % (ACTUAL_TEMP, MAX_TEMP)) }
 <br />           TEMPERATURE_WAIT SENSOR=extruder MAXIMUM={ MAX_TEMP }
 <br />       {% endif %}
<br />    {% endif %}

<h2>CANCEL and END MACROS </h2>
Check Your Positioning on these Macros. Mine for instance has G1 X10 Y490 F6000, Y490 is too MUCH with the new config. These will either be in your Macros.CFG or Printer.CFG
<br />
<br />[gcode_macro CANCEL_PRINT]
<br />G1 X10 Y475 F6000
<br />
<br />[gcode_macro PAUSE] 
<br />{% set y = params.Y|default(470) %}      #edit to your park position
<br />
<br />[gcode_macro PRINT_END]
<br />G1 X10 Y475 ;Present print


