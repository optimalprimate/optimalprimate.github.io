---

layout: post

title: "ESP32-based Online Pet Treat Dispenser"

date:  2022-06-22 20:31:36 +0100

categories: projects

---
# Streaming treats from a phone

![cat_feeder_final](/images/cat_final_open.jpg)

I recently discovered the 28BYJ series of stepper motor - they run on 5V, so no need for a 12V separate power supply (although they should be powered separately to the microcontroller) and they're remarkably good value (I bought a few for around £2/each).

This led to me wanting to build a treat dispenser for the cat, and it'd be cool if I could see the cat too, so I could use it remotely when out of the house.

I started thinking about a sort of Ferris-wheel approach, but after a casual look on Thingiverse, I actually found user 'CptPicard' had already made [a decent model](https://www.thingiverse.com/thing:3051036) of an Auger-based system with a hopper and 28BYJ motor mount.

So I decided to run it through the 3D printer and it came out looking pretty perfect, barring some sanding down of points where my supports had attached.

## Parts

- 28BYJ 5V Stepper
- ULN2003a driver board (usually supplied with the motor)
- ESP32-CAM
- MicroUSB Female Socket
- 3D printed Auger/hopper/motor amount
- Plywood & Hinges/Magnets

## Circuit

![cat_circuit](/images/cat_circuit.png)

The circuit consists of a MicroUSB socket which makes it easier to power with a choice of cable (providing enough amperage is given to the motor). I soldered this to a row of headers as power bus for 5V and Ground. I could've added data and hooked it up to the ESP32 for programming, but went with OTA updates instead.

If someone were to replicate this, you'd probably want a capacitor over the power supply for the motor and a diode to prevent backflow from the motor spinning...

Here is the circuit hooked up with simple dupont wires ready to be inserted into the box.
![cat_circuit_assembly](/images/cat_circuit_assembly.jpg)

This was a test run of the motor chucking out treats:  
<details>
  <summary>**click here to show**</summary>

<img src="/images/cat_poc.gif">
</details>

## Code

The code is uploaded [here](https://github.com/optimalprimate/cat_feeder).

This is similar to my other MQTT-based sensors and gizmos. It connects to Wifi, then the MQTT server to send and receive messages.

It includes:
- The basic ESP32-CAM examples of a simple server to stream video from the camera to a local IP address
- A callback that takes messages sent to a topic and tells the motor to go to that number of steps
- The callback will also take any messages starting with a '0' as a speed instruction, so it can be sped up
- A heartbeat message that publishes a message every 10 minutes to tell the server it's alive (and keep it connected)
- The typical ArduinoOTA script for pushing code over the network instead of plugging it in

On its own, the box won't do anything, so I used my NodeRed backend to:
- Build a dashboard page to control the feeder and view the stream
- Have a 'Give Treat' button that moves the stepper forward 1500 steps (enough to give a few treats)
- Stop the video stream if desired

My simple control dashboard:

![cat_ui](/images/cat_ui.jpg)


## Build

I went with a basic 3mm plywood box, very roughly approximated on the table saw and then lumped together with hot glue. Not pretty, but does the job. Dremel with a router bit made holes for the micro USB socket and the camera. Spray painted cat symbols may add some level of understanding what it's for. Not for the cat though.

A few small hinges give the lid support...

![cat_hinges](/images/cat_hinges.jpg)

...while two small magnets glued in place hold the lid closed and give a satisfying click shut. This is the same process as used on the [Retro TV](https://optimalprimate.github.io/projects/2021/02/03/RetroTV_Build.html), applying CA glue to the magnets and then closing the lid to put them in the right place.

![cat_magnets](/images/cat_magnets.jpg)

The USB power bus was hotglued into the base, and the ESP32Cam glued in place at the front (I'm hoping if I re-purpose this, the glue just snaps off...)

![cat_in_box](/images/cat_into_box.jpg)

And here it is finally assembled.

![cat_final2](/images/cat_final2.jpg)

![cat_final1](/images/cat_final1.jpg)


And a GIF of it in action with the phone-controlled NodeRed page:
<details>
  <summary>**Click Here to Expand**</summary>

<img src="/images/cat_final_run.gif">

<img src="/images/cat_run_open.gif">
</details>
