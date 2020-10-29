---
layout: post
title:  "Smart Mirror"
date:   2020-10-01 20:31:36 +0100
categories: projects
---
# A nicer way for data display
I first came across the Magic Mirror project years ago, where people were posting all kinds of these 'smart mirrors' online and fancied the idea, but didn't have much data to populate it with until recently. With the advent of my home automation network, I now have nodes gathering thousands of datapoints for temperature, lights, power, motion events, etc. This seemed the time to build the mirror to display some of this data, along with online sources (calendars, news, weather, etc.).

//IMAGE FINAL//

# Parts used
- Raspberry Pi 3B+
- Dell 19" monitor
- Two-way mirrored acrylic
- PIR sensor
- 5V Fan
- Power transformer (240AC to 5DC)
- Wood 'n wires


I have various old Pi's lying around from old projects. I thought about using a Pi2, but I always had issues with WiFi dropout with a dongle, so the built in networking of the Pi3 was perfect. A pizero would also do, but can't run the MagicMirror software itself and would need to serve it from an external server. 

The mirror portion should be of the two-way variety. Contrary to popular belief, those mirrors in interrogation rooms are in fact two-way, not one-way. They simply allow the brighter room to be visible and the darker room to be mirrored. This works in the smart mirror approach as we assume the monitor backlight will be the brightest thing in the frame. I used piece of mirrored acylic platic from Amazon as it was about £15 instead of over a hundred for glass. But glass will provide a much better, less distorted reflection.

So basically, you build a box to hold the monitor, stick the acyclic to it and then have a Pi at the back connected via HDMI.

## The Build

So I started with cutting my acyclic to size for the monitor. The best and cheapest tool I know of to cut straight acyclic is a 'scriber' which is like a hooked blade that you score the plastic with until you can just snap it.

![scribing](/images/mm_cutacryl2.jpg)
![scribing2](/images/mm_cutacryl.jpg)

I then stuck it to the monitor with black electrical tape. As the monitor will appear diffuse the further away from the 'mirror' it is, I took the bezel off the monitor first.

![mirror_stick](/images/mm_screen_tape.jpg)

A quick test everything was visible with the 'no signal' splash.

![mm_test1](/images/mm_screen_test.jpg)

## Woodwork.

I like the idea of wood but have little experience - so this was a good chance to learn.

I bought a sheet of 0.9mm plywood as it has a stainable hardwood layer on the outside and the perpendicular grain in each layer allows cutting it in any direction while maintaining strength.

The only saw I used for this was an electric jigsaw, so I cut strips for the edges first. I wanted to try bevel cuts, so set the saw to 45deg and cut the ends into a wedge. Finally gluing it together to make 'neat' corners.

![wood_edges](/images/mm_cutframe.jpg)

For the front, it was easier to cut a sheet the size of the monitor and just cut out the hole where the mirror would sit (i.e. not making a 4-part frame around it). This only really worked in wood as it was plywood, solid wood would most likely be too weak at the thin, tall edges.
I glued this front panel edges and clamped for a few hours.

![frame](/images/mm_rig_glue.jpg)

I wanted a darked colour, so got out some old stain and painted it all.

![frame](/images/mm_rig_stain.jpg)

Turns out, you can't stain the edges of plywood, as the layers that are running perpendicular (the end cuts) will just messily abosrb it, so I learnt about adhesive edging - a sort of strip of veneer that you iron on. As it's wood, it can also be stained.

![edging](/images/mm_edging.jpg)

Finally, I cut a backing board (I spray painted the edges black as was too impatient to stain) and the frame was pretty much done.

![frame_complete](/images/frame_comp.jpg)

## The Backend

I needed some space behind the monitor so cut out some of the plastic backing with a circular saw on a dremel. Messy, but quick.

![monitor_cut](/images/mm_cutmon.jpg)

I wanted a little extra functionality on this mirror, so I added in a PIR motion sensor to switch the display off when no one was near it, and a small computer fan that would come on to cool the system if it got too hot. Monitors can get warm after while, so didn't want to cook the Pi in a sealed, wooden crate.

I first drilled a hole on the underside of the mirror for PIR (so it's your legs & feet that trigger the mirror, but at least the sensor isn't obviously visible) and then a series of small holes for the fan vent. I put the fan on top as I figured heat rises and it would be a good place to extract it. It's also near the Pi that way.

![PIR_FANholes](/images/mm_drill.jpg)

I also needed to power the whole thing. The monitor obviously takes mains voltage and I decided I didn't want a separate power cable running up to the mirror carrying the 5V the Pi needs. So I found a transformer for the job that would fit in the monitor cut outs. I went for a 5V 2A output, which isn't ideal for the Pi3B (Raspberry state is should have 2.5A+), but since I had no real peripherals to power, I hoped it would do. Also the 3A transformer was too thick to fit, so no choice really.

I used quick connecters to splice into the monitor's power cable - **don't play with mains voltage if you don't know what you're doing**.

![mm_power](/images/mm_electric.jpg)

Here's the layout of the monitor, transformer, Pi, PIR and Fan:

![layout](/images/mm_layout2.jpg)

I hot glued it all in place straight to the back of the monitor display - luckily didn't damage it...

For the backing board to attach, I used the four mounting holes on the monitor as well as installing a few right angle brackets on the iside of the frame. To drill the holes for the bolts to line up with these holes, I used a dremel drill press to first line up with the metal brackets, and then square the backing on and drill straight through.

![drillbrackets](/images/mm_backholes.jpg)

## Software

