# Klipper Firmware for FLSUN QQ-S Pro
Firmware configuration files and instructions on how to get the [Klipper](https://www.klipper3d.org/) firmware running on the FLSUN QQ-S Pro delta 3D printer

The QQ-S Pro uses a board very similar to the MKS Robin Mini, but shares the same pinout as the MKS Robin Nano V1.

This repository is to help people get the Klipper firmware up and running on their QQ-S Pro delta printers.

## What is Klipper?

Klipper is a 3D printer firmware that runs on the Raspberry Pi as opposed to on the microcontroller of the printer.
The advantage of this is that the complex inverse kinematic calculations of delta printers can be processed on the
Raspberry Pi, which has way more computational horsepower compared to the 32-bit microcontroller in the printer.
This way, all the microcontroller has to handle are the movement (ex. move stepper_a 10 steps) as well as heating commands
(ex. heat extruder to 200 degrees). This results in a much smoother operation of the printer.

Another advantage of Klipper is that you don't have to recompile and flash the firmware every time you change something in 
the configuration file, such as Marlin. You can even edit the configuration file on the web interface (more about this later)
and hit a button to reset and load the new configuration. This makes tuning much more efficient.

Hardware you will need:

1. Raspberry Pi w/ SD Card
2. QQ-S Pro Delta Printer
3. Computer (PC,Mac,Linux)

Software you will need:

1. Klipper firmware for printer (.bin file)
2. Klipper client for Raspberry Pi
3. Web interface (OctoPrint, Mainsail, Fluidd)
4. Moonraker API (if using Mainsail or Fluidd)

## Step 1 - Setting up the Raspberry Pi

The heart of Klipper is the Raspberry Pi, which will be running both the firmware and the web interface through which we control the printer.
These are the steps to set up the Pi:

1. Download [OctoPi](https://octoprint.org/download/)




## Step 2 - Installing the firmware on the printer

I've included a pre-compiled version of the printer firmware in the **precompiled_firmware** folder. Simply drag this file into the
root directory of the SD card of the printer, insert it, and power it on. It is the same process as flashing Marlin or any other firmware on the QQ-S.

If you'd rather compile it yourself, this is what you
