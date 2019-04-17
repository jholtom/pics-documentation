# The Elysium Radio
- Schematic and Board Files can be found in private Dropbox. This is for intellectual property purposes.  BYU /technically/ doesn't maintain a license for the design anymore, since it came from a folded company, it is sort of in a gray area, since we paid for the built boards, and the folded company handed us all of the IP upon dissolution.  The next generation replacement board is entirely free of any potential licensing issue, and is called `Phoenix`. Request access to either design through Jacob Holtom (jacob@holtom.me)
- PDF Schematics can be found in `ELYSIUM_REV_C_SCH.pdf`

## Software
The software that runs on the Elysium radio originally started at a company called Adamant Aerospace, run by a man named Andrew Wygle.  Notes from him and artifacts he created can be found throughout the code and test scripts.  That company folded, and I (Jacob) inherited the board and software.  The software received several needed upgrades and modifications.
The core of the software is an open-source RTOS called ChibiOS.  It is currently using a relatively old base of the software.  It was ported to the MSP430 by Andrew Wygle, and so uses all of his HAL libraries.  The SPI libraries, external FRAM library and pretty much anything else that interfaces to the world was written by Andrew.  The most important libraries the ones found in `elysium-firmware/ChibiOS/os/ex/Semtech`.  They are `sx1212` and `sx1278`.  Each one refers to the Semtech chip it interfaces with.  They have a myriad of idiosyncrasis and potential pitfalls or problems.  I will go through them as best I can in the following sections.
### Hardware Drivers
#### SX1212 (sx1212.c)
- This is the chip used as the receiver.
#### SX1278 (sx1278.c)
- This chip is used as the transmitter.
- An important thing to note, the actual chip used is an `SX1276` and not an `SX1278`.  This is really just a clerical thing, but good to remember when reading the datasheet for the chip.  The library is called `sx1278.c` but the chip we use is an SX1276.  The primary difference that we care about is 
### Operating System Threads, Libraries and Behavior
#### UARTThread (UARTThd)
- This thread is fired up at boot, it handles all of the input/output of the UART bus.
#### RFThread (RFThd)
- This thread is also started at boot, it listens to a Mailbox for packets to transmit, and for a signal to go high, and also starts listening to the sx1212 interrupt lines to go high and the FIFO to be full.
#### CmdThread (CmdThd)
- This thread waits on a Mailbox for packets to come in over the UART or the RF bus and will handle them.  This handles commands to the Elysium radio core to set different parameters as well as get different parameters.
#### Network Layer (aka SPP and SDLP)
-  This implements a very barebones version of the CCSDS SPP (https://public.ccsds.org/Pubs/133x0b1c2.pdf) and SDLP (https://public.ccsds.org/Pubs/132x0b2.pdf and https://public.ccsds.org/Pubs/232x0b3.pdf) specification.
    - ##### SDLP
        - It handles the basics of the TM and TC protocols, TM used only for output from the Elysium, and TC only for input to the Elysium over the RF bus.
        - It only implements the RS part of the TM Synchronization and Channel Coding Protocol (https://public.ccsds.org/Pubs/131x0b3e1.pdf).
        - It instead uses the syncword for convolutional coding though...which is 0x1ACFFC1D  (For transmitting from Elysium)
        - It doesn't implement any of the TC Synchronization and Challen Coding Protocol (https://public.ccsds.org/Pubs/231x0b3.pdf)
        -For the receive syncword we use an arbitrary one, which is 0x8AD8BB2A (For trasnmitting to Elysium)
    - ##### SPP
        - It handles most of the SPP protocol.
        - It does not handle data blocks broken across multiple packets.
#### FRAM
- This initializes an I2C subsystem to interact with the external FRAM.  It also sets up functions to read and write to it either in an interrupt enabled state, or not.  It takes care of the threading nature of an RTOS and how it handles access to memory.
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
