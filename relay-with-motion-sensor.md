# Relay Switch With Motion Sensor

Description:  Behaves just like normal relay on Tasmota, but with added benefit that if it's off, it will turn on when motion
is detected and then turn off 10 seconds later.  It will not turn off it was originally on.

Note: The motion sensor middle pin goes to high when motion is detected, and then back to low a couple seconds later.  

Tasmota v9.2 flashed onto Sonoff Basic R2
HiLetgo AM312 Motion Sensor

## Assembly:
Soddered on 4-pin header onto Sonoff to facilitate flashing and connection to PIR sensor.
Straightened the 3 sensor pins and removed the plastic on the 3-pin header, cut the pins so they were
just long enough to connect to standard female connector.  Sensor middle pin connected to Sonoff
TX (GPIO 1) pin, 3.3V power and ground connected accordingly.  Used standard female-female connectors
with about 1.5-inch wire to connect Sensor to Sonoff.  Drilled hole in top of Sonoff case so that mushroom
shaped sensor would fit - hole is width of nushroon 'stem' so 'cap' is just a bit wider.  You end up with the
cap sticking out of the case, and works pretty well!

##Tasmota Config:

### GUI Config:

  Module->GPIO 1: switch_n (2)

###Command Config:

switchmode2: 1 (follow)

### Rule Psudo code:
save power state into var1
If motion (switch2 transitions to 1), trigger 'poweronmotion' event set to power status (var1).
If poweronmotion event fires with value of 0 (power is off), turn power on and start 10s timer
Turn off power when timer expires
Do nothing if switch2 transitions to 0 (so that power doesn't turn off 2 sec after motion)

### Rule Code:
on switch2#state=1 do event poweronmotion=%var1% endon on Power1#state do var1 %value% endon
on event#poweronmotion=0 do Backlog power on; RuleTimer1 10 endon on Rules#Timer=1 do power off endon
On switch2#state=0 do var2 0 endon
