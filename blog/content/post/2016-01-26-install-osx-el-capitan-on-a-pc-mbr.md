---
title: Install OSX El Capitan on a PC (MBR)
author: Bill Farmer
type: post
date: 2016-01-26T11:36:38+00:00
url: /install-osx-el-capitan-on-a-pc-mbr/
categories:
  - Hacking
keywords:
  - hackintosh
  - el capitan
  - osx
  - usb
  - mbr
  - gpt
  - clover
---
![OSX El Capitan][1]

### Prerequisites

  * A real Mac or Hackintosh
  * A spare disk drive &#8211; at least 8Gb, preferably USB
  * An 8Gb or larger USB stick
  * The Install OSX El Capitan.app from the App Store
  * A disk partition to install in

### Install on spare disk drive

![El Capitan Installer][4]

Connect your spare drive to your PC. Use the method described [here][5] to build the El Capitan install USB stick and install OSX using the GPT partition scheme on your spare disk drive. Check it boots up OK and works OK.

### Copy to your MBR partition

![Disk Utility][6]

Use the Disk Utility to either copy the El Capitan partition to your MBR partition, or create a disk image and restore it to your MBR partition. Install Clover on your new El Capitan partition using the MBR options.

![Clover][2]

Copy the kexts you used to build the USB stick to the new El Capitan partition in EFI>CLOVER>kexts>10.11. I needed FakeSMC.kext, NullCPUPowerManagement.kext and a ethernet driver AppleRTL8169Ethernet.kext. Disconnect the spare disk and reboot to check the El Capitan installation boots OK and works OK. If it worked on your GPT disk then it should be OK on MBR with the same drivers.

![Screenshot][3]

The problem with creating a GPT partitioned disk and installing on it is that you end up with a bios boot menu item &#8216;Mac OSX&#8217; which you can&#8217;t delete, even using the Clover EFI shell or the linux utility efibootmgr. I&#8217;ve got two flavours of linux and efibootmgr refuses to work on both of them. It may be possible to get around this by installing on a USB drive.

I have since found you need to run the Clover EFI shell while doing a UEFI boot to get access to the bios boot menu items.

 [1]: images/2016/01/El-Capitan.png
 [2]: images/2016/01/Clover.png
 [3]: images/2016/01/Screenshot.png
 [4]: images/2016/01/os-x-el-capitan-installer.png
 [5]: http://eladnava.com/install-os-x-10-11-el-capitan-on-hackintosh-vanilla
 [6]: images/2016/01/Disk-Utility.png
