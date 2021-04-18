# Unison
Open Source protocol for DAW <-> Hardware communication. Uses 11 bits instead of 7 bits (MIDI standard) for values.

## Introduction & Motivation
The Unison protocol aims to unify a fragemented landscape of DAW <-> hardware communication. There are MIDI based protocols (like, MCU, HUI) that are somewhat accesible, but limited in its usefulness, since they only transmit 7 bits of data (a range of 128).

Meta transmissions regarding the connection itself always consist of 3 bytes, the first starting with 

## Opening a connection
To request a device connection, the host sends three bytes to the device:

`80 ff ff`



## Ping
To keep a connection alive, the Unison host needs to ping the connected device at least every 2 seconds. To ping a device, the unison host sends these three bytes:

`80 00 00`

The device should respond with these three bytes:

`80 00 ff`
