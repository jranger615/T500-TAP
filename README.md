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
position_endstop: -25
position_min: -25
position_max: 500

<h2>probe changes</h2>

[probe]
pin:!PE8
x_offset: 0
y_offset: 0
#z_offset: -1.42
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
