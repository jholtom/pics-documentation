# The Space Segment
The space segment consists of the radio and its interaction with the flight computer
## Flight Computer - SOM
The SOM runs a custom kernel module called `ccsds-kernel-drivers` written by Jacob Holtom, that implements (to a degree) the CCSDS Space Packet Protocol (SPP) standard in kernel as an Address Family.  This allows it to be used with the standard UNIX socket interface.  Reference code can be found in the repository at `github.com/jholtom/ccsds-kernel-drivers`.  The most recent version for use on the satellite is in the `linux-2.6.33` branch.  The `byuspacecraft` organization maintains another version, that is more up to date for the PICS mission, and eventually that code will be merged back into the repository under `jholtom` (NOTE: as of 03/07/2019: This has not been done)
    - A linux kernel module is an extension of core functionality to the linux kernel.  It is implemented in pure C.
    - The UNIX socket interface is a standard set of functions to interact with any sort of network data.  A good API Reference is here: https://en.m.wikipedia.org/wiki/Berkeley_sockets

## Radio
The radio used is an (BYU/Jacob Holtom/Dr. Doran Wilde/Adamant Aerospace) Elysium. Notes on it can be found in `elysium.md`

## Antennas
There are two antenna assemblies used on the PICS mission.
### Transmit
The transmit antenna is a canted dipole configuration.  There is a copper tube cut to the appropriate length on adjacent corners.  They are fed in phase from a power divider board.  This power divider board uses an off-the-shelf divider chip.  Each antenna receives about 1W of power.  This results in a near omnidirectional pattern around the cube.  There is measured data provided courtesy of L-3 Communcations Systems West.  Data and Products can be found in the `antennas` folder.
### Receive
The receiving antenna is an exceptionally boring monopole.  It is a single little copper tube cut to length, and then wired directly into the radio via some RG-178 coax.
