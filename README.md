# PICS Documentation
This repository contains a dump of everything Jacob Holtom ever knew about PICS communications subsystem
It is a good place to start.

## Modeling
The link model used for the PICS Communications system came from the amateur radio communities repository of crazy excel spreadsheets.  A working version can be found here in PICSLinkModel.xls

## The PICS Communications Interface

### Transmit (to Ground - from Elysium)
- The Elysium Radio must have its Reset Pin high and 5V applied.  The transmit antenna must be attached (including power splitter).
- The Elysium Radio receives a message from the SOM over the UART bus (115200 baud default, maximum 1MBps)
- The Elysium Radio parses the message and it passes through the Network Layer router (`nl_route`)
- We will assume this message is destined for the ground
    - In `nl_route`, this means that the message is not destined for the elysium firmware, so it is given to `pass_func` which is a function pointer to the appropriate passing function for whichever bus delivered the message.  In our case, it points to `rf_pass` since it came in from UART and needs to pass onto the RF side.
    -
- The packet is already valid SPP as transmitted from the SOM, so it needs to just be put on the Link Layer (SDLP)
- The packet is passed to the RF Thread for handling. `RFPost` places the message into a Mailbox and alerts the SDLP (Link Layer) to handle it.
- The CCSDS SDLP Link Layer handles the message with `elyRFDLLBuildFrame`.  This function wraps the SPP packet in the SDLP frame, and performs the reed solomon encoding on it.
- This then triggers the `RFTxFrameReady` signal, and the RF Thread writes the `tf_buffer` into the SX1278's FIFO.
- The SX1278 is configured for transmission on thread startup, so it is already to go and transmit.
- The RF Thread triggers the transmit signal, which has the DAC write out the appropriate value to the power amplifier bias `DACWrite`.
- Once the packet is finished transmitting, the DAC is written back to 0, disabling the power amplifier.
- The message now travels through space to the ground station.
- On the ground station, the LNA must be powered on, the GNURadio GSReceive flowgraph, and everything powered up and aiming at the satellite.
- The signal enters the USRP B210, and is sent into the flowgraph through a source block
- GNURadio takes center stage
    - Data enters the flowgraph through a USRP source block tuned to the appropriate frequency.
    - Then it is bandpass filtered with the bandwidth of the modulated waveform plus some margin
    - It is then fed to a GMSK demodulator with the appropriate parameters.
    - Once it is passed through the demodulator, the sync word is searched for.
    - Once found it drops a marker in the data stream, and picks out the next 2040 bytes to be fed to the Reed Solomon Decoder
    - It is descrambled, deinterleaved and the reed solomon decoding is done.
    - This is then fed into the SDLP handler, which performs the deframing checks.
    - Then it is fed into the SPP router.
- The flowgraph emits the packet from the router into the appropriate ground station applications UDP socket.
- The ground station application receives the packet's message over UDP and handles as appropriate.

### Receive (from Ground - to Elysium)
- The ground station needs to be powered up, the GNURadio GSTransmit flowgraph, and the power amplifier enabled and current turned up. 
- We will assume the Elysium is already in the given conditions from the Transmit section.
- A message is transmitted from a ground station application into the UDP socket of the GNURadio Flowgraph.
- GNURadio takes center stage
    - Data enters over UDP and is immediately fed to
    - ....
    - the proper preamble is inserted into the stream
    - This chunk is then fed to a GMSK modulator
    - This is then fed into the USRP Source block which converts it into an analog signal
- Once the signal has left the USRP, it is fed into an 100 W power amplifier.  This power amplifier drives a dipole antenna feeding a 10ft dish.  This dish is then pointed at the satellite with Hamlib (rotctl)
- Once the message has travelled through the air to the satellite, it is incident upon the receiving antenna.
- The antenna is hooked up to an LNA.  This LNA provides healthy front end gain.  The LNA feeds into a SAW filter tuned at the receving frequencyes (459 MHz) and provides plenty of rejection.
- The signal then hits the SX1212 chip, where the syncword is detected.  On syncword detection, the SX1212 begins streaming data to the MSP430 (triggering an interrupt).  This data comes into the function `elyRFDLLHandleRxFifo`.
- That data is then handed off to the SDLP handler function, which performs a CRC check on the incoming message
- Once that is done, it is handed off to the Network Layer router (`nl_route`)
- We will assume that this message is destined for the SOM.
    - In `nl_route`, this means that the message is not destined for the elysium firmware, so it is given to the `pass_func` which is a function pointer to the appropriate passing function for whichever bus delivered the message.  In our case, it points to `uart_pass` since it came in from the RF chain, and needs to be passed onto the UART side.

### Direct Elysium Communication
- This is mostly the same as going from the ground to the SOM, with the exception of the APID being directed at the Elysium as opposed to the SOM.  The `nl_route` function will catch this and direct the packet to the Elysium.
