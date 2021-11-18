---

layout: post



title: "3D SLA Printer Remote Control Centre"



date: 2021-11-17 20:31:36 +0100



categories: projects



---

# 3D printing is a great tool, but needs some observation and temperature regulation. Remotely.

![title_image](/images/3d_CC_front_image.jpg)

After using filament printers about 10 years ago, I recently when down the SLA route - the printers using a vat of UV-curing resin to extrude structures of high resolutions, without the structural weaknesses of layers of filament.

I've been using the Elegoo Mars 2, which is a very cost-effective way to set up a small printing station in the corner of a (well-ventilated) room - or in my case garage.

During summer, this works fine, however a key determiner of success is the temperature of the resin and FEP film (a transparent sheet of flexible plastic that UV light can shine through, but prints shouldn't be able to stick to as they cure). The ideal temperature for this kind of printing is around 25°C, and with the printer being in a garage, this requires some external 'help'.

When a print gets going, it's also important to keep an eye on progress, as you want to abort it as soon as a failure occurs to save time, resin and a lot of cleanup afterwards.

So I set about building a remote monitoring device with the following requirements:

1) A remote camera that could show how the print was doing

2) A temperature sensor that could tell me what the resin temperature is at

3) The ability to turn on/off a space heater that can bring the temperature up if needed



For this, I settled on the following part list:

- ESP32-CAM - a wifi-enabled microcontroller (~Arduino) with built in camera and flash LED

- MLX90614 - a contactless temperature sensor, using IR to detect temperature remotely, [here](https://www.ebay.co.uk/itm/313603433717)
- 433MHz RF Transmitter - I used the superheterodyne variant, like [this one](https://www.ebay.co.uk/itm/262093895600?_trkparms=ispr%3D1&hash=item3d060377b0:g:upEAAOSwXHJgz41N&amdata=enc%3AAQAGAAACoPYe5NmHp%252B2JMhMi7yxGiTJkPrKr5t53CooMSQt2orsStdebXPz4ZTXCT8FI9kPBh2%252FqiiWUu3UdGhtthsj%252FdXv9NDjCaLwxjevdRt2XKi7lI6E5XRe6EZVSWZf7dJiDHmfNTuu8PeexLGiwkOfsgY0ncrFecBWiFqcZ3oUXQNYYZzZyOp80Q3iHxY1khHWKXdsxDEWGnmlcbvAQDsbfZuhmM4RfqmLdQdmQWET50xYMjEKl7xJLvZM7rMuKhVz0dwW4ZPFBpZw%252FT9PP6gZvfp5UVq5p15kinHgjhTfQ94EwZ5quTpyXtIQz3pQNAeHTnUUqnLcsX6nNA3KWddwWhp585Xkpny0LM%252BphR6DJm28ljUGaBh1qu8llIjJaW%252F6ubkBGODS%252BH76OTc2dU9hG769wqufLy3L7Y5s5GCrVZC9e7NgSRw6iOG17iYNRX5%252B7giQmMCK4c44YnWUsWEOJR5bBH6DCsBZQ3fKGQ9BpvwnlIygD8JKIePfoWMUeKal1HK89Sv7T%252Bfh9AAvr5C94pnpAGt4Gcub4yxzm7bPkID2h0wJboG5ZgO7kTyGFupEb%252FVYXqB%252Fk2eyJS2sl3ucm27qeb6sZ5JBE438vX6c%252BkvpQPzV4UshivmUCIT9lg0G453%252Bf70FLcoYF9SsjigM64%252F4O9L7Qnyz9dHcagbpXDjTjpNU2ZSdzReS06PrisZWw1%252FGgOn1Xjuemk2l2wG1fau9Q6KW%252BegG0P%252F7Ghb08Cjyi%252BSZ3DSWiRf2x26tnj5zwsvfo8T1xR45XK92LMsZEty07JsfGDrt3KBijYFbwUBpYKdy8E3UOK8OPBH1dSON912tJTmHMiX7zU6p7xh8T%252F02JmWXQTuVm5WT%252FeteHwzwxKtZMGvYLXA97MpJx8TDfFA%253D%253D%7Cclp%3A2334524%7Ctkp%3ABFBM7IuorqZf)

![parts_list](/images/3d_CC_parts_list.jpg)

In total, this came to about £15, and it could be halved if you're willing to wait longer for delivery.

In addition, I have an Amazon basics space heater and an old RF remote control plug socket (like [these](https://www.amazon.co.uk/DEWENWILS-Control-Wireless-Programmable-Sockets/dp/B07NJ2XHZC/ref=sr_1_5?keywords=remote+control+socket&qid=1637236646&sr=8-5&x=0&y=0)) to which I've already decoded the on and off codes using a simple RF receiver and an arduino. This makes it much easier to interface with mains-powered equipment as you don't have to start wiring relays, and you can keep the whole thing remote (and re-use the components for something else later).

I should mention that there is an excellent opensource project called [OctoPrint](https://octoprint.org/) that uses a Raspberry Pi based system to monitor printing remotely and issue commands to the printer. However, at the time of writing, SLA printer support was a little lacking and I wanted to mostly learn how to interface with the ESP32-CAM and IR sensors.

## Wiring & Assembly

![wiring](/images/3d_CC_wiring.jpg)

I cut the end off an old USB cable and soldered the ESP32 5V and Ground pins to the red and black wires. This provides easy power via a USB charger. 

The temp sensor is I2C, so you can use any GPIO pins and configure them in the code. When choosing GPIOs, be wary of what's already dedicated to using the camera and flash (flash LED is on GPIO4 btw, and is needed here as the printer is kept in the dark). There are also pins reserved for the SD card reader on the ESP32, but we're not using that so can steal them for the IR or RF modules.

I soldered the whole thing onto a small piece of perf board to keep the form as small as possible, with an antenna attached to the RF Transmitter.

![raw_module](/images/3d_CC_raw.jpg)

As the contactless temperature sensor will need to be moved around to point at the resin, I added slightly longer wire to give it positionability.

Since it's being used on a printer, I knocked up a case in Fusion360, mainly to protect it from resin splashes.

![printed_box](/images/3d_CC_printedbox.jpg)

## Code

The code is available [here](https://github.com/optimalprimate/3d_printer_control_centre/blob/main/3Dprint_ESP32_CAM_Therm.ino).

It's a straightforward connection to WiFi and MQTT server with callbacks based on the commands needed to control it. 

The inputs are to take picture or turn the heater on and off, while the outputs are sending an image over MQTT (this is great for ease of use, but limits file size) as well as reporting ambient and resin temperatures every minute.

There is a function for taking the picture that sets the flash LED pin high to snap the image and then low to turn it off.

I brought this into Node Red and knocked up a dashboard to control the camera and heater, and to check the temperature readings.

## Camera Tuning

In order for the camera to focus close up, the focus needs to be adjusted by rotating the lens. My ESP-32-CAM board had a big glue blob holding the focus and I had to do a lot of scraping and twisting to get it free, but I've had other boards where you can break the seal with little force. 

<details>
  <summary>**Expand to see lens rotation**</summary>
  
<img src="/images/3d_CC_camtune.gif">
</details>

I also had to add a delay in the code so the flash was on for 300ms before the camera took an image, otherwise the ESP didn't seem to have time to correctly expose the image without blowing it out.

You can see here a screen recording off my phone with the NodeRed controls/image at the top, with a video at the bottom of the ESP taking a picture with the flash.


<details>
  <summary>**Expand to see camera with flash in action**</summary>
  
<img src="/images/3d_CC_cam_flash.gif">
</details>

Finally, I added the option in the dashboard to save the images locally to convert to a timelapse if desired. The images aren't pretty, but you can see quickly if a print is failing or is even present on the buildplate.


<details>
  <summary>**Expand to see timelapse**</summary>
  
<img src="/images/3d_CC_timelapse.gif">
</details>


## Temperature Monitoring

In NodeRed, it's simple to output the temperature readings to a InfluxDB and plot them in Grafana. This adds a useful logging function of how temperatures can fluctuate. Interestingly, the photocuring of resin is exothermic, and so once the print has started, the heater is no longer needed and the temperature is self-sustaining.

![grafana_plot](/images/3d_CC_grafana.jpg)

It also straightforward in NodeRed to add a thermostat function, whereby if the temperature of the resin exceeds 27°C, the heater shuts off automatically. I also added a notification via Telegram to my phone if the ambient temperature exceeds 30°C in case the heater has failed to switch off.



