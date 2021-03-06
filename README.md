# Limitations
* I don't hook the IR service yet, so no zl/zr or c-stick. However, it is why I started this, so I'll be looking into it.
* Home and Power cannot be mapped the same way.  They are processed by the [MCU](https://3dbrew.org/wiki/Hardware#Auxiliary_Microcontroller_.28MCU.29).

## Credits
Memory patching code taken from BootNTR

Shinyquagsire for his writeup on redirecting input over WiFi.

Stary & Kazo for InputRedirector, upon which almost all of this is based.

## How to Use
Install the ButtonSwapNTR cia on your 3ds.

Not really configurable right now, but can be changed if you know ASM.

## Compiling/Configuring

The easiest way to build this is to use the provided docker-compose file in [the docker directory](docker/).
Clone this repository, make sure you have Docker installed (Install instructions for [Windows](https://docs.docker.com/docker-for-windows/install/) and [MacOS](https://docs.docker.com/docker-for-mac/install/)). Then, open a terminal/command prompt in the directory you cloned this repository into, `cd` into docker/, then run `docker-compose up`. Docker will build the image which contains all of the dependencies, then run make in the repository.

### Old/non-Docker build environment setup
You will need a working setup of the devkitARM toolchain for 3DS, follow [this guide](https://www.3dbrew.org/wiki/Setting_up_Development_Environment) for help.

You will also need the [ScenicRoute](https://github.com/Stary2001/ScenicRoute) library by Stary2001.  Clone it, then run make install in the ScenicRoute directory.
Finally, clone this repository.

The instructions for each type of mapping are provided in [source/injected.s](../master/source/injected.s).  I've provided a Java program to generate the correct button masks and coordinate values.  When you have saved your mappings into this file, you will need to compile it.  To build, change to the ButtonSwap3DS directory in a terminal, then run make.
### Each data field, and how to get it
#### Button Masks
This is the data that defines which buttons will activate the remapping and which buttons will be pressed.  Either use the provided program, or manually calculate the mask using [this table](https://www.3dbrew.org/wiki/HID_Shared_Memory#PAD_State).
For example, this code would swap A and B:
```
ldr r4, =0x1
ldr r5, =0x2
bl .button
ldr r4, =0x2
ldr r5, =0x1
bl .button
```
##### The next two data fields are six digit numbers.  The first 3 digits are the Y coordinate data, and the last 3 digits are the X coordinate data.
#### Touchscreen Data
The data that will be sent as the touchscreen.  Use the provided tool to generate this data.
#### C-pad Data
Data that will be sent as the c-pad.  This data is a bit harder to calculate.  The default value for the C-pad is ```0x800800```.  To calculate this value, you will need to use the developer mode on your calculator.  Xor ```0x800800``` by the value you want the C-pad to have.  For example, if you want to have the C-pad pushed to the right, you would xor ```0x800800``` by ```0x800FFF```, giving you ```0x7FF```.  Pad this with 3 zeroes in front (```0x0007FF```), and you have your data!
