# Relay Switch With Motion Sensor

This custom device Behaves just like a normal Sonoff R2 Basic on Tasmota, but with added benefit that if it's off, it will turn on when motion
is detected and then turn off 10 seconds later.  It will not turn off it was originally on.

The motion sensor middle pin goes to high when motion is detected, and then back to low a couple seconds later.  

## Stuff Needed
1. Sonoff Basic R2
2. HiLetgo AM312 Motion Sensor
3. 3 female-female wires (typical arduino kit wire)
4. DIY tools: soddering iron, electrical tape, wire cutters, flux, sodder, etc.
 
## Assembly:
Soddered on 4-pin header onto Sonoff to facilitate flashing and connection to PIR sensor.
Straightened the 3 sensor pins and removed the plastic on the 3-pin header, cut the pins so they were
just long enough to connect to standard female connector.  Sensor middle pin connected to Sonoff
TX (GPIO 1) pin, 3.3V power and ground connected accordingly.  Used standard female-female connectors
with about 1.5-inch wire to connect Sensor to Sonoff.  Drilled hole in top of Sonoff case so that mushroom
shaped sensor would fit - hole is width of nushroon 'stem' so 'cap' is just a bit wider.  You end up with the
sensor cap sticking out of the case, and it works pretty well!

## Tasmota Config

### GUI Config

Module->GPIO 1: switch_n (2)
MQTT = I set this up to integrate with my HA Mosquitto broker and set it up in HA as a light, so I have normal HA control of it.

### Command Config

switchmode2: 1 (follow)

### Rules Pseudo code:
save power state into var1
If motion (switch2 transitions to 1), trigger 'poweronmotion' event set to power status (var1).
If poweronmotion event fires with value of 0 (power is off), turn power on and start 10s timer
Turn off power when timer expires
Do nothing if switch2 transitions to 0 (so that power doesn't turn off 2 sec after motion)

### Commands
```
switchmode2 1
rule1 on switch2#state=1 do event poweronmotion=%var1% endon on Power1#state do var1 %value% endon
rule2 on event#poweronmotion=0 do Backlog power on; RuleTimer1 10 endon on Rules#Timer=1 do power off endon
rule3 On switch2#state=0 do var2 0 endon
backlog rule1 on; rule2 on; rule3 on
```
