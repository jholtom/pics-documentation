# The Elysium Radio
- Schematic and Board Files can be found in private Dropbox. This is for intellectual property purposes.  BYU /technically/ doesn't maintain a license for the design anymore, since it came from a folded company, it is sort of in a gray area, since we paid for the built boards, and the folded company handed us all of the IP upon dissolution.  The next generation replacement board is entirely free of any potential licensing issue, and is called `Phoenix`. Request access to either design through Jacob Holtom (jacob@holtom.me)
- PDF Schematics can be found in `ELYSIUM_REV_C_SCH.pdf`

## Software
The software that runs on the Elysium radio originally started at a company called Adamant Aerospace, run by a man named Andrew Wygle.  Notes from him and artifacts he created can be found throughout the code and test scripts.  That company folded, and I (Jacob) inherited the board and software.  The software received several needed upgrades and modifications.
The core of the software is an open-source RTOS called ChibiOS.  It is currently using a relatively old base of the software.  It was ported to the MSP430 by Andrew Wygle, and so uses all of his HAL libraries.  The SPI libraries, external FRAM library and pretty much anything else that interfaces to the world was written by Andrew.  The most important libraries the ones found in `elysium-firmware/ChibiOS/os/ex/Semtech`.  They are `sx1212` and `sx1278`.  Each one refers to the Semtech chip it interfaces with.  They have a myriad of idiosyncrasis and potential pitfalls or problems.  I will go through them as best I can in the following sections.
### Hardware Drivers
#### SX1212
#### SX1278
- An important thing to note, the actual chip used is an `SX1276` and not an `SX1278`.  This is really just a clerical thing, but good to remember when reading the datasheet for the chip.
### Operating System Threads, Libraries and Behavior
#### UARTThread (UARTThd)
This thread is fired up at boot
#### RFThread (RFThd)
#### CmdThread (CmdThd)
#### Network Layer (aka SPP and SDLP)
#### FRAM
#### ADC and DAC
There is an ADC (Analog to Digital Converter) and DAC (Digital to Analog Converter) on the board.  The ADC is meant to be used to read in the current power levels of the final RF drive amplifier.  The DAC is (currently) used to control the final output power of the RF drive amplifier. It is used by including the `adc.h` header and calling `DACWrite(<int value here>);`. The integer value is then modified and calibrated with a Power Meter or Spectrum Analyzer by measuring the burst power output of the output stage.  The ADC code should work to read in values, but has never been tested or used.  It is self-explanatory by reading the code in `adc.h` and `adc.c`

## Using it
### Bootloading and Firmware
You will first need to install the toolchain to compile the firmware for use.  A guide on how to build the toolchain can be found in `elysium-firmware\README.md`.  It also covers how to build and the necessary adjustments that need to be made when building for any given PIC or other satellite
### Programming for Flight
### Operation

## Hardware
The hardware that makes up the Elysium radio is a modified board designed by myself and Dr. Doran Wilde.  It is based on two Semtech SX12xx series chips.  The microcontroller used is an MSP430FR5969.  This particular MSP430 is based on FRAM, which makes it semi-desirable as it is more resistant to the effects of stray ions and so forth in space.  This however can be hotly debated by chip designers, whatever.  It exposes two interfaces to the user, one a 6-pin UART and Power header, along with one GPO pin.  The other is a 2-wire SPI bus used to JTAG and control the microcontroller.
### Transmitting

### Receiving
