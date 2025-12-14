---
title: SIM800L Integration with Raspberry Pi 3
summary: How to quickstart using SIM800L module with RPi3
date: 2019-12-17

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

In this past few days, I bought some IOT modules since I have a lying RPi3 around. I bought this RPi3 around 2016 to make a media box. Since I'm not using it as much as of now, I decided to tinker with it. One of the module that caught my attention was SIM800L module. It's only cheap, so I guess why not?

So here's my TL;DR in setting it up.

* Download and install the latest copy of Rasbpian to microSD.
* Setup RPi to be able to use serial pins.
* Connect the SIM800L to RPi GPIOs.
* Test the serial connection to the module.

Here's the needed peripherals to be able to finish the setup.

* RPi3
* MicroSD (16GB)
* SIM800L Module
* 5 Female-to-Female pins
* SIM Card

## Part 1

To start, go and download the latest image of Rasbian from Raspberry official site. Just download the Raspian Buster Lite version, or go with the desktop one if you're planning to connect it to TV or monitor.

[https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/)

After downloading, extract it. Then flash it to your flash disk. You can just use "dd" image utility, since it's pretty easy. Please take note that everything will be overwritten on the microSD card.

```
dd if=[RASPBIAN IMAGE] of=/dev/[MICROSD CARD] bs=4M
```

After flashing it, go and put it to the RPi. It would be much better if you can connect the RPi to monitor or TV for some initial setup. Upon checking, it seems that SSHD service is not enabled by default. You would need to enable it for easier access to the Pi. It would also be best if you can connect it to your network via ethernet port.

## Part 2:

Go boot your RPi. Once the server has been successfully booted, login into the server using this credentials.

* Username: pi
* Password: raspberry

After that, go and set-up these things below.

* Enable SSHD service.
* Upgrade the system to the latest version
* Disable serial console of the RPi.
* Enable UART.

Run the command below to enable sshd and do system upgrade if needed.

```
sudo systemctl enable sshd && sudo systemctl start sshd
sudo apt-get update && sudo apt-get upgrade -y
```

To disable the serial console, go run the command below. Then go to Option 5 - Interfacing Options. Go to P6 - Serial. Disable both the login shell, and the serial port hardware.

```
raspi-config
```

To enable UART, add the 'enable_uart=1' parameter to '/boot/config.txt&' file.

After setting everything up, go and power-off the RPi.

## Part 3

Get your 5 pins. There are 5 pins that should be connected from the SIM800L module to RPi. Just make sure that RPi is off before connecting the pins. Be better sure than having a fried RPi board. Connect the SIM card as well before putting the pins.

* 5V Input (SIM) > 5V Pin (RPi)
* Power Ground (SIM) > Ground (RPi)
* RX Pin (SIM) > TX Serial Pin - GPIO 14 (RPi)
* TX Pin (SIM) > RX Serial Pin - GPIO 15 (RPi)
* Data Ground (SIM) > Ground (RPi)
* I'm not really sure about the RST Pin on the SIM800L, but it seems that it's not needed yet.

> [!NOTE]
> There's a lot of recommendation to have a separate power supply for SIM800L module, but since I don't have those tools yet, I dared to connect it directly to RPi GPIOs. I didn't have any issues yet as of writing this post. Separate power supply might be needed if you're planning to connect to Internet while the RPi is up.

> [!NOTE]
> Actually tested using the GSM module with Gammu and PPP, and it didn't work as much. After testing with separate power supply, the module work as expected. It does need separate power supply.

Once everything is connected, go and power it on. Once the RPi is on, the module's light should be on. There's a separate blinking light indicator for the network connection.

## Part 4

You need to confirm now if the SIM800L is responding to serial commands. To test it, I just use a pyserial module from Python. You can also use minicom tool, but I think I'm having issues using it.

You can download the needed module using the command below.

```
sudo apt-get install -y python3-pip && sudo pip3 install pyserial
```

Once downloaded, you can use this code to test the serial connection. Save it as 'at-command.py'.

```
#This is the pyserial module
import serial

# Enable Serial Communication
# You may need to change the ttyS0 device and baudrate, but it worked in my case.
port = serial.Serial("/dev/ttyS0", baudrate=115200, timeout=1)

# Transmitting AT Commands to the Modem
# '\r\n' indicates the Enter key
port.write(b'AT\r\n')
rcv = port.read(10)
print(rcv)
```

Then run the script using the command below.

```
sudo python3 at-command.py
```

The command should print something like this.

```
b'AT\r\r\nOK\r\n'
```

Once you confirmed that the serial command is printing OK result, you can now successfully connect to the SIM800L module!

Congratulations, you can now use the SIM800L module to connect to Internet via ppp or send and receive sms using gammu. For these use cases, I'll create another post for those.

## Troubleshooting Notes

The at-command.py didn't generate an OK result.

You might confused the RX and TX connections between the SIM800L module and RPi. You need to connect the RX of module to TX of RPi, and vice versa. It's easy to encounter this issue, since I experienced it myself.

## To Do

* I'll add setup photos later.
* Add the references links.
* Create another post for pppd and gammu setup.