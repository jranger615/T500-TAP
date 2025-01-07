 <H1>T500 TAP Guide for Stock HOT END</h1> 

> [!IMPORTANT]
>All changes ive made are based off previous cfg files using KAMP developed by Trist0ne via https://github.com/Trist0ne/T500-Configuration-Public/tree/main. Please make sure you start with his configurations before you continue or some of this may not work

<br />
I am providing instructions for a 3D Printed OptTap V2.4 RC8 or the CHAOTICLAB Voron Tap V2.0. Below I will list the details of Each
<br />
<br />
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
<li>Brown = 24v </li>
<li>Blue = Ground</li>
<li>Black = Signal </li> 
</ul>


<h4>OptTap V2.4 RC8</h4>
Cut your Sensor Wire off or Order new 4 pin connectos
Stock Fytec Kit Connections
<ul>
<li>Brown Wire > Red Wire </li>
<li>Blue Wire > Black Wire</li>
<li>Black Wire > Green Wire</li>
</ul>


<h4>CHAOTICLAB Voron Tap V2.0</h4>
Cut your Sensor Wire off or Order new 4 pin connectos
<ul>
<li>Brown Wire > Red Wire </li>
<li>Blue Wire > Black Wire</li>
<li>Black Wire > Yellow Wire</li>
</ul>

<h2>X EndStop Sensor Move</h2>
For this modificaiton, move the X end stop sensor from the back of the  X Axis, to the front bottom screw as shown. I used a washer or 2 to help center the tap Adapter extension that will trigger this sensor.
<img src="https://github.com/user-attachments/assets/97bec78c-74b7-47c7-9558-208e333e2462"</img>

<h2>Voron Tap Assembly</h2>
<img src="https://github.com/jranger615/T500-TAP/blob/main/VORON%20CNC%20STL/photos/Tap%20Assembly.jpg?raw=true"</img>
<h2>HotEnd Changes</h2>
<img src="https://github.com/jranger615/T500-TAP/blob/main/photos/HotEnd%20Changes.jpg?raw=true"</img>

<h2>Stepper_y changes</h2>

```ruby
position_endstop: -30 #adjustment for New Offset of Y Axis oddly this brings the hotend to the end of the thermal sheet. You coudl set this to 50 to get to closer to the front of the bed
position_min: -30 #adjustment for New Offset of Y Axis, oddly this brings the hotend to the end of the thermal sheet. You coudl set this to 50 to get to closer to the front of the bed
position_max: 503
```
<h2>probe changes</h2>

```ruby
[probe]
pin:^!PE8  #This was changed from Inital Sensors PE8. We invert the polarity (!) and set pull up (^)
x_offset: 0 #Adjust to 0 as Nozzle is now the sensor
y_offset: 0 #Adjust to 0 as Nozzle is now the sensor
#z_offset: -1.42 #Comment Out and Redo your Z_offset in calibration
speed: 10.0
samples: 3
samples_result: median
sample_retract_dist: 3.0
samples_tolerance: 0.006
samples_tolerance_retries: 10
activate_gcode:
    {% set PROBE_TEMP = 275 %}
    {% set MAX_TEMP = PROBE_TEMP + 5 %}
    {% set ACTUAL_TEMP = printer.extruder.temperature %}
    {% set TARGET_TEMP = printer.extruder.target %}

    {% if TARGET_TEMP > PROBE_TEMP %}
        { action_respond_info('Extruder temperature target of %.1fC is too high, lowering to %.1fC' % (TARGET_TEMP, PROBE_TEMP)) }
        M109 S{ PROBE_TEMP }
    {% else %}
        # Temperature target is already low enough, but nozzle may still be too hot.
        {% if ACTUAL_TEMP > MAX_TEMP %}
            { action_respond_info('Extruder temperature %.1fC is still too high, waiting until below %.1fC' % (ACTUAL_TEMP, MAX_TEMP)) }
            TEMPERATURE_WAIT SENSOR=extruder MAXIMUM={ MAX_TEMP }
        {% endif %}
    {% endif %}
```
<h2>MACRO CHANGES</h2>
Check Your Positioning on these Macros. Mine for instance has G1 X10 Y490 F6000, Y490 is too MUCH with the new config. These will either be in your Macros.CFG or Printer.CFG
<br />

```ruby
[gcode_macro CANCEL_PRINT]
G1 X10 Y475 F6000
```

```ruby
[gcode_macro PAUSE] 
{% set y = params.Y|default(475) %}      #edit to your park position
```

```ruby
[gcode_macro PRINT_END]
G1 X10 Y475 ;Present print
```

