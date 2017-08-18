---
title: Install OSX El Capitan on a PC (MBR)
author: Bill Farmer
type: post
date: 2016-01-26T11:36:38+00:00
url: /install-osx-el-capitan-on-a-pc-mbr/
categories:
  - Hacking

---
## [<img class="alignnone size-full wp-image-322" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/El-Capitan.png" alt="El Capitan" width="586" height="354" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/El-Capitan.png 586w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/El-Capitan-300x181.png 300w" sizes="(max-width: 586px) 100vw, 586px" />][1]

## Prerequisites

  * A real Mac or Hackintosh
  * A spare disk drive &#8211; at least 8Gb, preferably USB
  * An 8Gb or larger USB stick
  * The Install OSX El Capitan.app from the App Store
  * A disk partition to install in

## Install on spare disk drive

<a href="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/os-x-el-capitan-installer.png" rel="attachment wp-att-331"><img class="alignnone size-large wp-image-331" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/os-x-el-capitan-installer-1024x824.png" alt="os-x-el-capitan-installer" width="625" height="503" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/os-x-el-capitan-installer-1024x824.png 1024w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/os-x-el-capitan-installer-300x241.png 300w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/os-x-el-capitan-installer-768x618.png 768w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/os-x-el-capitan-installer-624x502.png 624w" sizes="(max-width: 625px) 100vw, 625px" /></a>

Connect your spare drive to your PC. Use the method described <a href="http://eladnava.com/install-os-x-10-11-el-capitan-on-hackintosh-vanilla/" target="_blank">here</a> to build the El Capitan install USB stick and install OSX using the GPT partition scheme on your spare disk drive. Check it boots up OK and works OK.

## Copy to your MBR partition

<a href="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Disk-Utility.png" rel="attachment wp-att-329"><img class="alignnone size-full wp-image-329" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Disk-Utility.png" alt="Disk Utility" width="740" height="641" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Disk-Utility.png 740w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Disk-Utility-300x260.png 300w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Disk-Utility-624x541.png 624w" sizes="(max-width: 740px) 100vw, 740px" /></a>

Use the Disk Utility to either copy the El Capitan partition to your MBR partition, or create a disk image and restore it to your MBR partition. Install Clover on your new El Capitan partition using the MBR options.

&nbsp;

[<img class="alignnone size-full wp-image-323" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Clover.png" alt="Clover" width="620" height="439" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Clover.png 620w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Clover-300x212.png 300w" sizes="(max-width: 620px) 100vw, 620px" />][2]

Copy the kexts you used to build the USB stick to the new El Capitan partition in EFI>CLOVER>kexts>10.11. I needed FakeSMC.kext, NullCPUPowerManagement.kext and a ethernet driver AppleRTL8169Ethernet.kext. Disconnect the spare disk and reboot to check the El Capitan installation boots OK and works OK. If it worked on your GPT disk then it should be OK on MBR with the same drivers.

[<img class="alignnone size-large wp-image-327" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Screenshot-1024x819.png" alt="Screenshot" width="625" height="500" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Screenshot-1024x819.png 1024w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Screenshot-300x240.png 300w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Screenshot-624x499.png 624w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Screenshot.png 1280w" sizes="(max-width: 625px) 100vw, 625px" />][3]

The problem with creating a GPT partitioned disk and installing on it is that you end up with a bios boot menu item &#8216;Mac OSX&#8217; which you can&#8217;t delete, even using the Clover EFI shell or the linux utility efibootmgr. I&#8217;ve got two flavours of linux and efibootmgr refuses to work on both of them. It may be possible to get around this by installing on a USB drive.

 [1]: http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/El-Capitan.png
 [2]: http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Clover.png
 [3]: http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/01/Screenshot.png