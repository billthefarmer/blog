---
title: Move Linux Mint Root Partition
author: Bill Farmer
type: post
date: 2015-08-31T13:33:24+00:00
url: /move-linux-mint-root-partition/
categories:
  - Hacking

---
I recently added a new disk drive to my workstation and, as part of reorganising, wanted to move the Linux Mint root partition from one disk to another. Naturally I ~~googled~~ duckied it and found a certain amount of useful info.

I used gnuparted to copy the partition from one disk to another. The standard method to reinstall grub on a partition so it will boot appears to be to mount it and use grub-install with the &#8211;boot-directory option like this.
<pre>
#mount /dev/dsc4 /mnt
#grub-install --boot-directory=/mnt/boot /dev/sdc
</pre>
However, because grub2 uses partition uuids to identify partitions, and because the new partition kept it&#8217;s uuid, it just booted the old location. So I created a new uuid and tried again:
<pre>
#uuidgen
2b12199a-84ed-4e3f-8339-48dadffac45b
#tune2fs /dev/sdc4 -U 2b12199a-84ed-4e3f-8339-48dadffac45b
#mount /dev/dsc4 /mnt
#grub-install --boot-directory=/mnt/boot /dev/sdc
</pre>
That didn&#8217;t work either as grub-install wouldn&#8217;t change the uuids in grub.cfg so the new partition would boot.

So I got evil, I used emacs to do a global search and replace on the old and new uuids in grub.cfg. That worked. Once the new partition had booted I did another grub-install to correct the hacked grub.cfg. What interests me &#8211; Is there a less evil way of doing this with grub2?
