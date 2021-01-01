# Making a robot vacuum cleaner _smart_

A few months ago I acquired a Eufy Robot Vacuum 11S cleaner when on offer. It's just a low profile 'robot' that wonders around aimlessly cleaning controlled via a remote control. Not being WiFi- connected nor app-controlled seemed to bring the price down substantially. As well as an opportunity to not stream personal data across the world.

![Eufy Vac](/images/vac_eufy.PNG)

The only issue is that while feeling very futuristic, the device responds only to its remote, and I wanted something a little more automatic as well as 'unlosable'. So I set about incorporating it into my home automation network - including the Google home voice assistant.

I started by identifying the remote control as infrared (as opposed to RF) by simply observing the large, darkened transparent plastic head.

I set up an Arduino Uno with an IR receiver and loaded in the standard IR-interpretation code from the [IRremote library](https://www.arduino.cc/reference/en/libraries/irremote/). As the system seemed to use a non-standard profile, I used the 'raw' codes.

These are essentially a series of numbers that represent the millisecond timings to flash the IR LED, and look something like:

`3000,2900, 550,450, 550,1400, 550,1400, 550,450, 500,1450, 550,450, 550,400, 550,450, 550,450, 550,400, 550,450, 550,450, 500,1450, 550,1400, 550,450, 500,1450, 550,450, 500,450, 550,450, 550,450, 500,1450, 500,1450, 550,450, 550,1400, 550,450, 500,450, 550,1400, 550,450, 550,1400, 550,1450, 500,450, 550,450, 500,1450, 500,1450, 550,1450, 500,1450, 500,1450, 550,1450, 500,1450, 550,1400, 550,1400, 550,450, 550,1400, 550,450, 500,1450, 500,1450, 550,450, 550,1400, 550`

_(this is just the command for turning it on)_

I've put the codes in a table [here](https://github.com/optimalprimate/Eufy_11S_vacuum_automation/blob/main/IR%20Codes) if anyone wanted them for future reference.

I then soldered an IR LED with an NPN transistor (as the current drawn from the IR LED is a little high for the pins of the ESP8266) to an existing ESP8266 'node' that was in the kitchen where the vacuum's charger is located. The nice thing about high-powered (5V) IR LEDs is that it doesn't really matter where they are in the room - the signal just bounces around and finds its target. 

![vac_pinout](/images/vac_schematic.jpg)

_ESP8266 in a Wemos D1 board with the NPN 2N2222 and an 880nm 5V IR LED_

A simple command from the [IRremote library](https://www.arduinolibraries.info/libraries/i-rremote-esp8266), IRsend, can then send the relevant code from the ESP8266 and this is then interfaced with the rest of the system via MQTT messaging over the WiFi network.

Once complete, I added it into the home system via Node-Red and created a NORA object switch that Google Home could interpret as a regular outlet. The 'on' position starts the vacuum cleaning, and the 'off' state tells it to go back to its charging dock. 

I put a routine into Google so if I say 'clean up' it cleans and 'recharge' tells it to dock. I also added buttons in the Node Red dashboard so I can control it locally, as seen in this video below, triggering the cleaning while away and observing over the MotionEye camera server running on the Pi:

<details>
  <summary><b>Click to View Clip</b></summary>
  
<img src="/images/vac_remote.gif">
</details>

_This is a mobile screen capture showing the remote camera above and the Node-Red dashboard below, tapping 'vac-auto' triggers the cleaner to slide out from under the sideboard and start cleaning_

For now, this is just a simple added functionality for remote and voice-controlled cleaning, but since the controls for directional commands were also easily interpreted, it opens up opportunities of chaining multiple directional commands to guide the cleaner to specific places or around a room (akin to the mapping function found in cleaners 3x the price of this one).



