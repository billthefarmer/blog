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

Smart meters are not a new idea. I was a member of a project in 1983
which designed and rolled out a prototype smart meter which was far
ahead of it's time. The Unit was called a [CALMU][0] (Credit and Load
Management Unit). It was capable of recording electricity consumption
with several tariffs, gas consumption, with a connection to a suitable
gas meter and water use, with a connection to a suitable water
meter. It had a remote display panel with a [vacuum fluorescent display][1]
which could be mounted in a suitable position.

The electricity meter used a variant of an [Intel 8051][2] processor
which included an eight bit [A-to-D][3]. To increase the accuracy the
measurements were made against a slowly ramping up and down base level
and aggregated over a time period. The samples were taken synchronised
with the supply frequency at various points on the wave. I argued at
the time that it would be more accurate to sample unsynchronised at a
small difference from the supply frequency so the sample points drifted
across the waveform over time. Who was right I have no idea.

The meter was connected to the customers' telephone line via a
multi-purpose modem/[radio teleswitch][4] receiver. The receiver used
two nested phase lock loops, the inner loop synchronised to the
message bits, the outer loop synchronised to the [50 bit messages][5]
(PDF). This was implemented by a ferrite rod aerial connected to a
simple transistor amplifier connected to an input pin on the
processor, an [Intel 8051][2] variant with an [EPROM][6]. Sychronisation to
radio teleswitch messages was achieved by a successful 13 bit crc
check and start bit.

The input from the telephone line was connected to an interrupt pin on
the processor. When an interrupt occurred due to an incoming message
from the telephone exchange the processor checked for a signal long
enough to check for a valid message and either processed the message
or went back to radio teleswitching. The modem used the [CUTS][7]
cassete tape standard for communication, which AFAIK was never
intended for use over telephone lines. However it worked.

 [0]: https://www.umsmeters.co.uk/contact-us/manuals-and-downloads/old-pri-meters-kit
 [1]: https://en.wikipedia.org/wiki/Vacuum_fluorescent_display
 [2]: https://en.wikipedia.org/wiki/Intel_MCS-51
 [3]: https://en.wikipedia.org/wiki/Analog-to-digital_converter
 [4]: https://en.wikipedia.org/wiki/Radio_teleswitch
 [5]: http://downloads.bbc.co.uk/rd/pubs/reports/1984-19.pdf
 [6]: https://en.wikipedia.org/wiki/EPROM
 [7]: https://en.wikipedia.org/wiki/Kansas_City_standard
