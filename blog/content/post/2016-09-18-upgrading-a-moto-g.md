---
title: Upgrading a Moto G
author: Bill Farmer
type: post
date: 2016-09-18T20:27:57+00:00
url: /upgrading-a-moto-g/
categories:
  - Hacking
keywords:
  - moto g
  - cyanogenmod
---
![Moto G][1]

I got myself a refurbished Moto G phone a while ago. It came with Android Kitkat 4.4.4, complete with Moto bloat. I had already put [Cyanogenmod][2] on my tablet, so I installed Cyanogenmod 11, android 4.4.4 on it, in accordance with the instructions on the [wiki][3], no problem. That got rid of the bloat and allows fine control of app snooping via Privacy Guard in the settings.

When I first installed Cyanogenmod, I had to use an unofficial build, as an official build for Kitkat 4.4.4 wasn&rsquo;t available. Now there are six official builds [available][4], including two for Marshmallow. I tried to upgrade to Lollipop using the built in upgrade system, but failed I think it complained about the bootloader.

So I downloaded the official factory image for Lollipop 5.1 from [here ][5] (XT1032\_FALCON\_RETGB\_5.1\_LPBS23.13-56-2\_cid7\_CFC.xml.zip) and attempted to install it. I followed the instructions [here][6], which appeared to work ok, but the phone wouldn&rsquo;t boot. Fastboot appeared to be erasing the system partition each time I loaded another sparsechunk file.

So I looked in the flashfile.xml, which appears to contain the instructions for flashing and found a couple of extra commands, which stopped fastboot erasing the partitions. This doesn&rsquo;t appear to be in the xdadevelopers forum thread, or I couldn&rsquo;t find it. So the commands for flashing the phone are:

<pre>
mfastboot oem fb_mode_set
mfastboot flash partition gpt.bin
mfastboot flash motoboot motoboot.img
mfastboot flash logo logo.bin
mfastboot flash boot boot.img
mfastboot flash recovery recovery.img
mfastboot flash system system.img_sparsechunk.0
mfastboot flash system system.img_sparsechunk.1
mfastboot flash system system.img_sparsechunk.2
mfastboot flash system system.img_sparsechunk.3
mfastboot flash modem NON-HLOS.bin
mfastboot erase modemst1
mfastboot erase modemst2
mfastboot flash fsg fsg.mbn
mfastboot erase cache
mfastboot erase userdata 
mfastboot oem fb_mode_clear
mfastboot reboot
</pre>

This worked fine and the phone booted up into Lollipop ok. Having done that, I was able to install Cyanogenmod (cm-12.1-20151117-SNAPSHOT-YOG7DAO1K9-falcon.zip) without too much difficulty. I also found a copy of the MotoCamera app [here][7], which I installed, and a replacement [boot logo file][8] which replaced the annoying unlock warning boot message. It probably wasn&rsquo;t necessary to install all of the factory images, just installing the updated partition, motoboot, logo and recovery images would have allowed me to install the latest Cyanogenmod .

![Screenshot][9]

 [1]: images/2016/09/Moto_g.jpg
 [2]: http://www.cyanogenmod.org
 [3]: https://wiki.cyanogenmod.org/w/Install_CM_for_falcon
 [4]: http://download.cyanogenmod.org/?type=snapshot&device=falcon
 [5]: http://www.filefactory.com/folder/c6cdedc45a775d27
 [6]: http://forum.xda-developers.com/showthread.php?t=2542219
 [7]: http://forum.xda-developers.com/devdb/project/dl/?id=15250
 [8]: http://forum.xda-developers.com/showthread.php?t=2548530
 [9]: images/2016/09/Screenshot_2016-09-18-21-23-45.png
