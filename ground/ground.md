# The Ground Segment

## Software Defined Radio (SDR)
The SDR we use is called the ETTUS USRP B210. Ours is configured to use a GPSDO (GPS Disciplined Oscillator) for its clock. It feeds a powerful desktop computer with digitally converted RF data. On the desktop computer we run GNURadio. There are two flowgraphs, one called GSReceive, one called GSTransmit. GSReceive receives all modes of communication from the satellite. GS Transmit handles all transmissions to the satellite. GSReceive and GSTransmit both implement an SPP packet router. They interact with the other parts of the system through standard UDP sockets. 
### GSReceive
	The flow graph begins with a USRP source block. This is then fed to a band pass filter. After the band pass filter, it feeds into a GMSK Demodulator. That block feeds into a sync word detection system. Once the appropriate slync work is detected an SDLP frame is chunked off and fed to the deframer. After the deframer, the spp router directs packets to the appropriate destination.
### GSTransmit

## Antennas
	On the roof of the Clyde Building there are 5 spots for 10 foot dish antennas.  These come from leftover Radio Astronomy projects.  Thank Dr. Jeffs for the use of the equipment, since they have moved on to bigger and cooler things.
### Receiving Antennas
For the receiving dish we use an off the shelf patch antenna to feed the 10-ft dish. It is tuned in the 900 megahertz band. it runs into an LNA and filter mounted on the back of the patch antenna.This is then cabled across the roof and down to the control station. This cable run has about 9db of loss. The LNA produces 40 db of gain. This antenna on top of a 10ft dish develops about 27 db gain with about an 8 degree beamwidth.
### Transmitting Antennas
	The transmit antenna is an off the shelf dipole tuned in the 450 megahertz band. This antenna is used as the feed one of the 10-ft dishes. It develops approximately 10 db gain with a very fat main lobe.  There is a spare dipole feed laying around in the PICS Ground Station area.
### Rotators 
	We have 5 M2 RC2800 rotators. They are controlled by M2 AZ1000s and EL1000s. The rotators azimuth and elevation are controlled independently. They are driven by a program called GPredict. This program has been superseded by the SATNOGS network and client applications. SATNOGS aka Cygnus use pyephemera to predict satellite locations. On the day of launch we need to get the kepler two line elements from NORAD and input them to the SATNOGS DB application.  We have modified the SATNOGS network and client code to become Cygnus, which is what I've called the compendium of ground station applications.  The primary extensions allow for multiple antennas to be controlled simultaneously as we are a full-duplex system with two independent bands. 
### Power amplifier 
	The transmit antenna is driven by an 100 watt power amplifier. This power amplifier comes from Richardson RFPD. A custom heat sink and fan system have been built. It is controlled by an Arduino. Power for this amplifier is provided by two Astron power supplies. Care needs to be taken to ensure the 100 watts does not leak all over the 5th floor. The cable currently being used is appropriately shielded.

## Cygnus (SATNOGS Network)
### Network
### DB
### Client
