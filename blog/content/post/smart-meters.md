---
title: Smart Meters
author: Bill Farmer
type: post
date: 2018-07-14T21:22:37+01:00
categories:
    - Electronics
keywords:
    - meter
    - smart
    - 8051
    - 8049
    - radio
    - teleswitch
---

#### CALMU
Smart meters are not a new idea. I was a member of a project in the
early 1980s which designed and rolled out a prototype smart meter
which was far ahead of it's time. The unit was called a [CALMU][0]
(Credit and Load Management Unit). It was capable of recording
electricity consumption with several tariffs, gas consumption, with a
connection to a suitable gas meter and water use, with a connection to
a suitable water meter. It had a remote display panel with a [vacuum
fluorescent display][1] which could be mounted in a suitable position.

#### Metering
The electricity meter used a variant of an [Intel 8051][2] processor
which included an eight bit [A-to-D][3]. To increase the accuracy the
current and voltage measurements were made against a slowly ramping
base level and aggregated over a time period. The samples were taken
synchronised with the supply frequency at various points on the
wave. I argued at the time that it would be more accurate to sample
unsynchronised at a small difference from the supply frequency so the
sample points drifted across the waveform over time. Who was right I
have no idea.

#### Radio teleswitch
The meter was connected to the customers' telephone line via a
multi-purpose modem/[radio teleswitch][4] receiver. The receiver used
two nested phase lock loops, the inner loop synchronised to the
carrier phase, the outer loop synchronised to the [message][5] (PDF)
bits. The messages were 50 bits long with a 13 bit CRC and a start
bit. This was implemented by a ferrite rod aerial connected to a
simple transistor amplifier connected to an input pin on the
processor, an [Intel 8051][2] variant with an
[EPROM][6]. Synchronisation to radio teleswitch messages was achieved
by a successful 13 bit CRC and start bit check.

![Crystal][11]

Clock crystal used for the radio teleswitch processor.

#### Modem
The input from the telephone line was connected to an interrupt pin on
the processor. When an interrupt occurred due to an incoming message
from the telephone exchange the processor checked for a signal long
enough to check for a valid message and either processed the message
or went back to radio teleswitching. The modem used the [CUTS][7]
cassete tape standard for communication, which AFAIK was never
intended for use over telephone lines. However it worked.

#### Data Concentrator
At the telephone exchange was a data concentrator which interrogated
the CALMUs on demand from the computer centre. It was an off the shelf
Intel 8086 development board in an Intel enclosure. The code was
written by the [CEGB][12] in some obscure real time language which was
probably popular at the time. However a telephone exchange was a high
electrical noise environment at that time as it was full of busy
electro-mechanical exchange equipment. So the concentrator crashed
regularly and had to be reset.

#### Display Panel
How the display panel communicated with the CALMU I don't remember
except that it used 4 core telephone wire and probably some sort of
serial protocol. I also don't remember how the modem processor
communicated with the metering processor.

#### Super Snooper
I did develop several useful gadgets for testing purposes, one was the
CALMU Super Snooper which had a two line [LCD][8] display and a
hexadecimal keypad. This allowed interrogating a CALMU by sending a
query message and displaying the reply. It used the EPROM variant 8051
processor. It had a piezo buzzer mounted on the pcb which provided
audio feedback for the keypad. If you held a button down while turning
it on it played the tune from Super Trouper by Abba.

![LCD Display][15]

Similar LDC display

#### BBC Micro receiver
Another was a radio teleswitch receiver using an 8051 variant which
was connected to a [BBC Micro][9] digital I/O port. This, in
conjunction with some software on the BBC Micro, enabled the radio
teleswitch messages to be displayed.

#### Acorn Atom terminal
The other was an EPROM that turned an [Acorn Atom][10] into a serial
terminal. The standard keyboard scanner in an Atom was horrible to
type on, it only registered a key press when you released the key. So
I wrote my own, included in the EPROM. I can't remember why we wanted
a serial terminal, but we must have done or I wouldn't have had the
idea.

#### Other projects
There were other metering projects going on at the time, one used
broad band communication down the customers' electricity supply cable
from the nearest substation. This worked during times of low demand on
the power network, but became unreliable during times of high demand
due to the high noise level.

#### Bitstream
The various utilities were all nationalised industries at the time, so
there was not too much difficulty working with other utilities on an
interesting project such as this. Shortly after the end of the project
[BT][13] rolled out a new tariff I think they called [Bitstream][14]
to offer communication with devices on a customer's premises via the
telephone line. The prices they wanted for the service were completely
out of scope for meter reading. So that was the end of that. Since
then all the utilities have been privatised, so any joint metering
projects became exceedingly unlikely. They have since all done their
own thing.

 [0]: https://www.umsmeters.co.uk/contact-us/manuals-and-downloads/old-pri-meters-kit
 [1]: https://en.wikipedia.org/wiki/Vacuum_fluorescent_display
 [2]: https://en.wikipedia.org/wiki/Intel_MCS-51
 [3]: https://en.wikipedia.org/wiki/Analog-to-digital_converter
 [4]: https://en.wikipedia.org/wiki/Radio_teleswitch
 [5]: http://downloads.bbc.co.uk/rd/pubs/reports/1984-19.pdf
 [6]: https://en.wikipedia.org/wiki/EPROM
 [7]: https://en.wikipedia.org/wiki/Kansas_City_standard
 [8]: https://en.wikipedia.org/wiki/Liquid-crystal_display
 [9]: https://en.wikipedia.org/wiki/BBC_Micro
 [10]: https://en.wikipedia.org/wiki/Acorn_Atom
 [11]: images/2018/07/P1020804.JPG
 [12]: https://en.wikipedia.org/wiki/Central_Electricity_Generating_Board
 [13]: https://en.wikipedia.org/wiki/BT_Group
 [14]: https://www.btwholesale.com/pages/static/products-services/data.htm
 [15]: images/2018/07/F5326385-01.jpg
