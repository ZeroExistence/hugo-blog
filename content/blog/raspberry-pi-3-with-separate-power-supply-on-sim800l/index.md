---
title: Raspberry Pi 3 with Separate Power Supply on SIM800L
summary: Need more power? Give it more power then!
date: 2020-01-05

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Joydeep Pal on Unsplash**](https://unsplash.com/photos/pie-with-raspberry-on-top-SyBVbjY5tFM)'

authors:
  - me

tags:
  - Tech
  - Raspberry
---

After initial testing with SIM800L without separate power supply, I explored a bit and bought additional components for my testing.

* DC Buck Converter
* 12V 1A DC Adapter
* DC Jack Barrel
* Alligator Clips
* Male to Female Jumper Wires
* Quad-band Wired Cellular Antenna
* Digital Multimeter

To set this all up, I assume that you already setup the GSM module with RPI3. We will just setup the power supply side of it.

## Part 1

Connect the DC barrel jack with the 12V DC Adapter. Connect the DC barrel jack to DC buck converter using alligator clips. Then plug the DC adapter to outlet.

Check if the buck converter is powered on after plugging in. It has a LED indicator, so it should lit up.

We should setup the voltage output of the buck converter to 5V. To do this, you will see a screw on top of the blue part of the buck converter. Use multimeter to monitor it's current voltage on the output, then turn the screw. Adjust it accordingly until it reads within ~5V area.

## Part 2

Plug out the power supply for now. Get some male to female jumper wires. Connect the female jumpers to 5VIN and GND. After that, connect the male jumper of 5VIN with the out+ of the buck converter. Connect the male jumper of GND to out-. For solderless setup, I just use an extra alligator just to keep the male jumper in place.

Once it's done, power-on your RPI3 first, then plug in the power of the GSM module. The GSM module should blink now, indicating that it's already power on.

> [!Note]
> If you noticed that the GSM module is suddenly turning off, then turn on, you might need to adjust the output of the buck converter. It usually happen when there's not enough power, or too much voltage to the GSM module.

## Part 3

Test the connection to the GSM module now. You should be able to connect to it seamlessly now.

## Part 4 (Bonus)

Since we have more power to our GSM, we should use that power to get more signal. To have more signal, use a longer cellular antenna. Just make sure that it has SMA connector.

To connect it, just remove the default antenna provided with GSM module, then put the new antenna.
