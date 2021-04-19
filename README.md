# Unison
Open Source protocol for DAW <-> Hardware communication. Uses up to 15 bits instead of 7 bits (MIDI standard) for values.

## Introduction & Motivation
The Unison protocol aims to unify a fragemented landscape of DAW <-> hardware communication. There are MIDI based protocols (like, MCU, HUI) that are somewhat accesible, but limited in its usefulness, since they only transmit 7 bits of data (a range of 128). Protocols like Eucon allow for more precise values of data, but are proprietary and in the case of Eucon, you are not allowed to build any control surfaces.

Unison tries to build up a protocol that is open source, goverened by the community and everyone is allowed to implement it in software and hardware. Our goal with Syra Audio is to empower people to create music in diverse ways and this is just another step towards that goal.

## Warning! Protocol in alpha state!
This protocol is in its very very early stages and **will change** alot! We do not recommend implementing it into a product until it hits a stable version. At Syra Audio, we will constantly implement it into our products to check if the protocol is missing anything. Of course you are allowed to do so as well, but use at your own risk.

## General overview
The protocol enables software and hardware manufactures (and enthusiasts) to build there own devices and apps that are compatible with eacht piece of soft- and hardware that implements this protocol.

Since the most obvious application to control is a digital audio workstation, the protocol focuses on enabling the needed functionalities for this software. In general audio software other than full fledged DAWs are often just a subset of a DAW and should therefore be compatible with the protocol.

The protocol is divided in a transport or main section that includes transport control general display of information and various other global functions. Since Unison isn't limited to 3 byte packages (like MIDI) the Unison protocol can cover way more functions.

The channels are divided into zones. To make it easy for manufacturers, the Unison protocol assumes a zone to be a max of 16 channels and a max of 16 zones (bundled into 1 byte). So the protocol supports a maximum of 256 channels. Each zone can have fewer than 16 channels, with different zones being completely independent from each other. So one zone can have two channels while another one has 16.

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

Transmissions consist of block of 4 bytes (so 4, 8, 12, 16, 20, etc. bytes), always starting with `80` or `8i` (where `i` is a 4 bit connection id) to identify the unsion protocol.

## Opening a connection

### From Host
To request a connection from a device, the host sends three bytes to the device:

`80 ff 00 00`

The device should answer with 8 bytes or two 4 byte blocks:

`80 ff ff mm mm mm pp pp`

The first 3 bytes are protocol specific, with `80 ff ff` stating that a connection will be established. The other 5 bytes are used to identify the device. To break it down a bit further:

* `80` - Unison protocol identifier
* `ff ff` - Connection will be established
* `mm mm mm (4th to 6th byte)` - Manufacturer ID
* `pp pp (7th and 8th byte)` - Product ID

The manufacturer ID `ff ff ff` is reserved for developing. A host should allow manufacturer IDs with the value `ff ff ff` if it contains a development mode and it is active.

#### How to ensure valid manufacturer and product IDs

Todo...

If the host validates the connection answer, it itself sends an answer:

`80 ff ff fi`

Where `i` is a connection id. This allows having multiple devices using the Unison protocol connected at the same time, controlling the same application. The device has to extract that id and keep it in memory. Since multiple devices can control the same application, the device has to send its connection id for the host to identify which device sent the command.

At this point the device should have its end of the connection ready and the host should start sending the ping in intervals to keep the connection alive.

### From Device

This follows the same routine as the connection establishing from the host side, but with a difference, that the device has to send a connection request message:

`8i 7f 00 00`

If the host recevices this message, it should start the connection routine as described above.

## Ping
To keep a connection alive, the Unison host needs to ping the connected device at least every 2 seconds. To ping a device, the unison host sends these 4 bytes:

`8i 00 00 00`

The device should respond with:

`8i 00 ff 00`

If the host fails to send a ping after 2 seconds relative to the last sent ping answer, the device should close its connection.
If the device fails to send a answer for the ping after 2 seconds the host should close its connection.

Now both the host and the device can send commands and values to each other to update the respective surface.

## Transport Controls

Transport controls 
