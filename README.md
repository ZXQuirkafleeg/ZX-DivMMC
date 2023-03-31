# ZX-DivMMC

This is a DivMMC clone [(link)](https://divide.speccy.cz/) for the RC2014 ZX128 Spectrum board [(link)](https://github.com/ZXQuirkafleeg/ZX128).  The VHDL is a port of the Xilinx code [(link)](https://github.com/mprato/DivMMC) to an Altera CPLD.

Notable features are:

* Support for 2 SD cards 
* Support for various ROM (28C64 and 28C256) and RAM (62256 and 628128) devices

![Front of ZX Development board with a prootype disk interface](https://github.com/ZXQuirkafleeg/ZX-DivMMC/blob/main/Images/DivMMC%20-%20System.jpg)

## ROM

The board can take either a 28C64 or a 28C256 EEPROM in U3.  For a 28C256 device, J4 connects to A13 and A14 to select an 8K page for the ROM image (the DivMMC design only supports an 8K ROM directly).  This is useful for switching quickly between DivMMC operating systems such as ESXDOS and FATware.  No jumpers are needed for a 28C64 device.

## RAM

The board can take 62256 or 628128 (or equivalent) RAMs in U4.  Jumper positions for J5 are:

Jumper/Device	 | 62256	| 628128
:---:          | :---: | :---:  							
PA	           | Left  | Right           
PB	           | -     | Right       

## SD Card module

The required SD card module is a standard arduino type with a 3V3-5V regulator and level converter.  They are available on AliExpress etc.

![SD Card module](https://github.com/ZXQuirkafleeg/ZX-DivMMC/blob/main/Images/DivMMC%20-%20SD%20Card%20Reader%20-%20small.jpg)

## VHDL Code

A Quartus 13.1 project can be found [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/tree/main/CPLD).  

## RC2014 Bus

There are some differences between the RC2014 bus and the Spectrum edge connector signals.  Notably the CPU clock is inverted on the edge connector.  Spectrum peripherals also use ROMCS and NMI (e.g. DIVMMC and Multiface), so these are brought out to the user pins on the RC2014 bus:

*	Pin 37 – User 1 – ROMCS
*	Pin 38 – User 2 – NMI 
*	Pin 39 – User 3 – (not connected, though later used for WAIT)
*	Pin 40 – User 4 – (unallocated)

## Build
All components are though-hole and standard density.  A schematic can be found [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/blob/main/PCB/ZX%20DivMCC%20-%20Schematic%20-%20V1.0.pdf) and a BOM [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/blob/main/PCB/BOM%20-%20DIVMMC%20-%20V1.0.csv).

### PCB 

For the PCB, EasyEDA (V6.5.22) design files can be found [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/tree/main/PCB) and gerbers [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/blob/main/PCB/RC2014%20-%20DIVMMC%20-%20V1.0.zip).  The PCB is a standard two layer board approx. 100mm by 60mm.  Decoupling capacitors for the CPLD are mounted under the socket.

### CPLD Programming

Programming the CPLD can be done by the Programmer app in Quartus 13.1.  Connect a USB Blaster dongle (or clone) to J4 and connect the 5V supply to the ZX DivMMC.  

### ROM Programming

A sample ESXDOS V0.86 ROM image can be found [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/tree/main/ESXDOS%20V0.86/ROM).  Programming the ROM can be done by using a TL866II or similar.  Please note the ROM image used should be the BIN file and not the RAW file - the former is a standard ROM image while the latter expect the address and data lines to be swapped as in the DixMMC design .  The DivMMC software is also capable of programming the ROM in situ.

### SD Card

Any standard microSD card, formatted with FAT32, should work.  A zip of the minimum system contents can be found [here](https://github.com/ZXQuirkafleeg/ZX-DivMMC/tree/main/ESXDOS%20V0.86/Software) and should be extracted to the root directory of the SDcard.  Further software and information can be found [here](https://divide.speccy.cz/) and [here](http://esxdos.org/index.html).

## Reset

R2 and C5 provide a power-on reset cct for the interface.  Changing C5 for a lower value - e.g. 47 or 22nF may improve system stability at power on.

## Jumpers and Connectors

* J1 – RC2014 Bus connector
* J2 – Extended RC2014 bus connector (not required)
* J3 – ROMCS and NMI jumper – used connect ROMCS to U1 and NMI to U2 of RC2014 bus
* J4 – EPROM 8K Page select
* J5 – RAM Device select
* J6 - EPROM jumper - connect if the EPROM is installed 
* J7 – Altera USB Blaster ICSP connector

## System Test

Programme the ROM, CPLD and create the SD card.  Jumper J3 so ROMCS and NMI are connected to the RC2014 bus, and J6 to indicate the ROM is instealled.  Programme the CPLD with the POF using Quartus.

Install the CPU, ZX128 and DivMMC in the backplane and apply power.  On powerup the screen should go black and the details of the SD card appear.  Holidng down space while resetting the CPU also forces a cold boot.  Pressing NMI (SW2) starts the ESXDOS interface.  Current draw of the board is around 130 mA making a total of 350 mA for the system.
