# Unison
Open Source protocol for DAW <-> Hardware communication. Uses up to 15 bits instead of 7 bits (MIDI standard) for values.

## Introduction & Motivation
The Unison protocol aims to unify a fragemented landscape of DAW <-> hardware communication. There are MIDI based protocols (like, MCU, HUI) that are somewhat accesible, but limited in its usefulness, since they only transmit 7 bits of data (a range of 128). Protocols like Eucon allow for more precise values of data, but are proprietary and in the case of Eucon, you are not allowed to build any control surfaces.

Unison tries to build up a protocol that is open source, goverened by the community and everyone is allowed to implement it in software and hardware. Our goal with Syra Audio is to empower people to create music in diverse ways and this is just another step towards that goal.

## Warning! Protocol in alpha state!
This protocol is in its very very early stages and **will change** alot! We do not recommend implementing it into a product until it hits a stable version. At Syra Audio, we will constantly implement it into our products to check if the protocol is missing anything. Of course you are allowed to do so as well, but use at your own risk.

## Roadmap

- [ ] Define base protocol
- [ ] Connection protocol
- [ ] Exchanging meta information
- [ ] Define behavior for transport controls
- [ ] Define behavior for Channel strips
- [ ] SDKs

## How this works
At Syra Audio we prototype this protocol using an Arduino Leonardo Micro Controller and its USB port with WebUSB. So we have some level of abstraction while prototyping. But with that comes a performance drain, so we definitely are aware of any performance bottlenecks and try to adress those. At the end of the day it is up to you how to implement this protocol. We recommend using some kind of serial connection, like USB 3.0 or Thunderbolt 3 / USB C to have a bit of an abstraction layer and a fast performance.

**All protocol values are given in hexadecimal.**

Transmissions consist of block of 4 bytes (so 4, 8, 12, 16, 20, etc. bytes), always starting with `80` to identify the unsion protocol.

## Opening a connection

### From Host
To request a connection from a device, the host sends three bytes to the device:

`80 ff 00 00`

The device should answer with 8 bytes or two 4 byte blocks:

`80 ff ff 00 00 00 00 00`

The first 3 bytes are protocol specific, with `80 ff ff` stating that a connection will be established. The other 5 bytes are used to identify the device. To break it down a bit further:

* `80` - Unison protocol identifier
* `ff ff` - Connection will be established
* `00 00 00 (4th to 6th byte)` - Manufacturer ID
* `00 00 (7th and 8th byte)` - Product ID

The manufacturer ID `ff ff ff` is reserved for developing. A host should allow manufacturer IDs with value `ff ff ff` if it contains a development mode and it is active.

#### How to ensure valid manufacturer and product IDs

Todo...

If the host validates the connection answer, it itself sends an answer:

`80 ff ff ff`

At this point the device should have its end of the connection ready and the host should start sending the ping in intervals to keep the connection alive.

### From Device

This follows the same routine as the connection establishing from the host side, but with a difference, that the device has to send a connection request message:

`80 7f 00 00`

If the host recevices this message, it should start the connection routine as described above.

## Ping
To keep a connection alive, the Unison host needs to ping the connected device at least every 2 seconds. To ping a device, the unison host sends these 4 bytes:

`80 00 00 00`

The device should respond with:

`80 00 ff 00`

If the host fails to send a ping after 2 seconds relative to the last sent ping answer, the device should close its connection.
If the device fails to send a answer for the ping after 2 seconds the host should close its connection.

Now both the host and the device can send commands and values to each other to update the respective surface. 

## Transport Controls

Todo...
