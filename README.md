# What is Klipper?

Klipper is a 3D printer firmware that runs on a Raspberry Pi as opposed to the microcontroller of the printer.
The advantage of this is that the complex inverse kinematic calculations of delta printers can be processed on the
Raspberry Pi, which has way more computational horsepower compared to the 32-bit microcontroller in the printer.
This way, all the microcontroller has to handle are the movement (ex. move stepper_a 10 steps) as well as heating commands
(ex. heat extruder to 200 degrees). This results in a much smoother operation of the printer.

Another advantage of Klipper is that you don't have to recompile and flash the firmware every time you change something in
the configuration file, such as Marlin. You can even edit the configuration file on the web interface (more about this later)
and hit a button to reset the printer and load the new configuration. This makes the tuning process much more efficient.

### Always use this online README as a reference since the troubleshooting section is constantly updated.

# Klipper Firmware for FLSUN QQ-S Pro
This repository contains firmware configuration files and instructions on how to get the [Klipper](https://www.klipper3d.org/) firmware running on the FLSUN QQ-S Pro delta 3D printer.

The QQ-S Pro uses a board very similar to the MKS Robin Mini, but shares the same pinout as the [MKS Robin Nano V1](https://github.com/makerbase-mks/MKS-Robin-Nano-V1.X/blob/master/hardware/MKS%20Robin%20Nano%20V1.1_001/MKS%20Robin%20Nano%20V1.1_001%20PIN.pdf).

## How to Connect an End Switch Style Filament Sensor
Open [this](https://github.com/makerbase-mks/MKS-Robin-Nano-V1.X/blob/master/hardware/MKS%20Robin%20Nano%20V1.1_001/MKS%20Robin%20Nano%20V1.1_001%20PIN.pdf) link and connect the filament sensor to 'MT_DET1' and use the printer(filament_sensor).cfg file.

## How to Connect a BL Touch
Open [this](https://github.com/makerbase-mks/MKS-Robin-Nano-V1.X/blob/master/hardware/MKS%20Robin%20Nano%20V1.1_001/MKS%20Robin%20Nano%20V1.1_001%20PIN.pdf) link.

1. Remove the WiFi Module
2. Switch the jumper pin from 3.3V to 5V (located near the top right of the WiFi module - from perspective of picture)
3. Connect:
   - Orange wire -> IO0-PA8
   - Brown wire -> GND
   - Red wire -> 3.3V
   - White wire -> Z- (PA11)
   - Black wire -> Z- (GND)
4. Use the corresponding printer(BL_touch).cfg file


# How to Install the Klipper Ecosystem


There are 4 different types of configurations for the QQ-S Pro in the *configurations* folder:

1. QQ-S Pro Stock
2. QQ-S Pro with Filament Sensor
3. QQ-S Pro with BL Touch
4. QQ-S Pro with BL Touch and Filament Sensor

Hardware you will need:

1. Raspberry Pi w/ SD Card
2. (optional) Webcam
3. QQ-S Pro Delta Printer
4. Computer (PC, Mac, Linux)

Software you will need:

1. Klipper firmware for printer (.bin file)
2. Klipper client for Raspberry Pi
3. Web interface (OctoPrint, Mainsail, Fluidd)
4. Moonraker API (if using Mainsail or Fluidd)
5. Kiauh Klipper installation and update tool (this tool will allow you to install, update, and remove all the software you will need)


## Step 1 - Setting up the Raspberry Pi

The heart of Klipper is the Raspberry Pi, which will be running both the firmware and the web interface through which we control the printer.
These are the steps to set up the Pi. I suggest either using the Mainsail or Fluidd web interface as OctoPrint doesn't have nearly as much control
over Klipper. Both Mainsail and Fluidd were developed for Klipper (Fluidd is a fork of Mainsail so they're pretty similar) However, if you are
hellbent on using OctoPrint, stop after **Step 6** below and skip ahead to **Step 9**:

1. Download the [OctoPi](https://octoprint.org/download/) operating system and set it up on the Raspberry Pi according to the instructions on the download page.
2. Once the Raspberry Pi is configured, ssh into it from your computer: `ssh pi@octopi.local`
3. Clone the Kiauh repository into the root directory of the Pi: `git clone https://github.com/th33xitus/kiauh.git`
4. Navigate to the Kiauh directory: `cd kiauh`
5. Run Kiauh: `./kiauh.sh`
6. Install Klipper Firmware
7. Install the Moonraker API
8. Install either the Mainsail **or** Fluidd web interface
9. (optional) Install 'MJPG Streamer' if using a webcam
10. Reboot the Raspberry Pi `sudo reboot`
11. Once the Pi reboots, Navigate to your selected hostname (default is 'http://octopi.local') in your browser. *I will assume you're using the     default hostname for the duration of this guide.*

You should see your selected web interface displayed on the page.


## Step 2 - Installing the firmware on the printer

This firmware is the same for all QQ-S Pro printers. The configuration is done
on the Raspberry Pi.

### Precompiled Version
I've included a pre-compiled version of the printer firmware in the **precompiled_firmware** folder.

1. Drag this file into the root directory of the SD card of your printer, insert it, and power it on. It is the same process as flashing Marlin or any other firmware on the QQ-S Pro.

### Uncompiled Version
If you'd rather compile it yourself or my precompiled version is not working for some reason, this is what you need to do:

1. ssh into your Raspberry Pi: `ssh pi@octopi.local`
2. Navigate to the klipper directory: `cd klipper`
3. Type: `make menuconfig` and select these settings:
- Enable extra low-level configuration options
  - Micro-controller Architecture (STMicroelectronics STM32)
  - Processor model (STM32F103)
  - Bootloader offset (28KiB bootloader)
  - Clock Reference (8 MHz crystal)
  - Communication interface (Serial (on USART3 PB11/PB10))
- (250000) Baud rate for serial port

4. Press `q` to quit and `y` to save your settings
5. Type `make` to compile the firmware
6. Next, navigate to the *scripts* folder: `cd scripts`
7. Run: `./update_mks_robin.py ../out/klipper.bin ../out/robin_mini.bin`

 - this will rename the firmware file from 'klipper.bin' to 'robin_mini.bin'
   and save it to the *out* directory)


8. Navigate to the *out* directory and verify that the file 'robin_mini.bin' exists: `cd ../out` then `ls`
9. Disconnect from your Pi: `exit`
10. Download the 'robin_mini.bin' file from the Raspberry Pi to your computer using sftp: `sftp pi@octopi.local` then `cd klipper/out` then `get robin_mini.bin`

  - this will download 'robin_mini.bin' to whichever directory you've issued the sftp command from


11. Drag this file into the root directory of the SD card of your printer, insert it, and power it on. It is the same process as flashing Marlin or any other firmware on the QQ-S Pro.


## Step 3 -  Connecting the Printer to the Raspberry Pi

1. Open the web interface `http://octopi.local` in your browser

2. Plug in the printer to one of your Raspberry Pi's USB ports

3. ssh into your Raspberry Pi: `ssh pi@octopi.local`

4. Find out which serial port your printer is connected to: `ls /dev/serial/by-id/*` and copy the output

5. Open up the printer.cfg file on your browser:
  - **Mainsail**: in the *settings/machine* tab on the bottom left
  - **Fluidd**: in the *printer* tab on the top right


6. Once in the printer.cfg file, delete everything and copy the contents of the desired config file from the *configuration* folder of this repository

7. Set your printers serial port:

  - find the `[mcu]` section
  - delete the path after `serial:` and paste your output from step 4


8. Press the 'Save & Restart' button, if successful you should hear the printer beep and take a couple seconds to connect

  - if your printer is not connecting, try pressing the 'firmware restart' button or typing `FIRMWARE_RESTART` in the console


9. Test the connection by homing your printer or typing `G28` in the console.


## Step 4 - Calibrating the printer

1. The first thing we need to do is connect the z-probe to the effector (the autolevel switch).

  - If using a BL Touch, follow [this guide](https://www.klipper3d.org/Probe_Calibrate.html) to calculate the X and Y offset of the probe before continuing.


2. Then open up the web interface console and type `DELTA_CALIBRATE`. Once this is done type: `SAVE_CONFIG` to save the settings.

  - This will get a decent calibration for your printer, good enough to print well. However if you really want to optimize the calibration
  for your specific printer, run the [Enhanced Delta Calibration](https://www.klipper3d.org/Delta_Calibrate.html) which requires you to print an
  object, measure it with digital calipers, and input the values in the console.


3. When the delta calibration is finished, you need to calculate the Z-offset by typing: `PROBE_CALIBRATE`

  - **After the probe stops**, do the [paper test](https://www.klipper3d.org/Bed_Level.html#the-paper-test) (place a piece of paper under the nozzle)

  - Type: `TESTZ Z=-<value>` to *decrease* Z-height and `TESTZ Z=+<value>` to *increase* it, where `<value>` is the amount in mm by which to decrease or increase the Z-height (usually 0.05 or 0.01 if close to the bed) so that there is just a little friction between the nozzle and paper.

  - When you are satisfied with the Z-height, type `ACCEPT` and then `SAVE_CONFIG`


4. Bed mesh leveling is enabled in all the config files, so after setting the Z-offset, type `BED_MESH_CALIBRATE` in the console then `SAVE_CONFIG`
   when finished.

   - If using an offset probe (like a BL Touch) you might get an error 'move out of range' because the probe offset causes the print head to move outside the range of the print bed. If this happens, try decreasing the 'mesh_radius' or offsetting the 'mesh_origin' in the config file.


5. Remove Z-probe

6. Tune the heater PID settings so that you don't over shoot your desired temperature: `PID_CALIBRATE HEATER=extruder TARGET=<temperature>` where `<temperature>` is the desired temperature to use for tuning.

## Step 5 - Print!

1. Copy `START_PRINT` and paste it in the **start g-code** of your slicer.
2. Copy `END_PRINT` and paste it in the **end g-code** of your slicer.

  - Both these macros can be found in the config file, so paste any start and end g-code originally found in the slicer underneath the `gcode:` line in both `[gcode_macro START_PRINT]` and `[gcode_macro END_PRINT]`, **so the only thing that should be in your slicer is `START_PRINT` and `END_PRINT`.** This way, if you need to add or delete any gcode commands, they can all be done in the config file without having to reslice the model.


3. Load some gcode and start a print


4. Both *Mainsail* and *Fluidd* allow you to adjust the z-offset while printing if the calibration didn't get it quite right. If you do this, this offset value (which is shown on screen) is reset after every print.
In order to make that value permanent you have to:

 - go back to `[gcode_macro START_PRINT]` and comment out the line:   `SET_GCODE_OFFSET Z_ADJUST=-0.1 MOVE=1` by removing the **#** in front of it.

 - Then replace `-0.1` with the offset value you got during printing. This way, it will load your specific offset every time you start a print.

 - The `END_PRINT` macro is equally important, because it resets the offset value after every print. If you don't add it to your slicer, Klipper will add your offset values after every print. Ex. *print1 offset=0.1, print2, offset=0.2,...* until you restart the printer.


Here is the Klipper [documentation](https://www.klipper3d.org/Overview.html) where you can find more info about configuring Klipper. The [config reference](https://www.klipper3d.org/Config_Reference.html) section is probably referenced the most.


# Troubleshooting

1. Why does my bed mesh look really weird?

    * Either your printer is not calibrated properly or your probe is not accurate enough

    * Try running `PROBE_ACCURACY` with your Z-probe attached and if you get a value greater than 0.25 mm, your probe is not accurate enough to use mesh leveling and you have to delete your mesh profile in the web interface.

    * If You get a value less than 0.25, then you need to run the [Enhanced Delta Calibration](https://www.klipper3d.org/Delta_Calibrate.html)


2. Why is my extruder temperature reading way off?

    * The wrong temperature sensor is probably set in the [extruder] section of the config file. The stock thermistor of the QQ-S Pro is `sensor_type: EPCOS 100K B57560G104`. Check out [the documentation](https://www.klipper3d.org/Config_Reference.html#temperature-sensors) for all supported temperature sensors.
