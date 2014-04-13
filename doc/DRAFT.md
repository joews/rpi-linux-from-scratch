# Raspberry Pi Linux From Scratch: What I did

Bookmarks:

http://www.linuxfromscratch.org/lfs/view/stable/
http://www.intestinate.com/pilfs/guide.html

I wanted to start from a new Linux installation, following the "new partition" method". I chose Arch Linux because it's small and lightweight.. 

Download latest Arch Linux RPI and validate MD5.

Backup old raspbian install:
Use diskutil  list to find which disk is the SD card. (disk1 for me). Then use rdiskn, which is faster:

http://raspberrypi.stackexchange.com/a/4213

sudo dd if=/dev/rdisk1 bs=1m | gzip > /Users/joe/personal/rpi-lfs/raspbian_20140413.img.gz

(took 7 mins, output from 4gb sd card 1.5gb)

Then write the new OS:

ALWAYS ALWAYS ALWAYS triple check the disk device. If you get the wrong one, you WILL overwrite your OS and brick your machine.

sudo dd if=ArchLinuxARM-2014.04-rpi.img of=/dev/rdisk1 bs=1m

First time round I got permission denied. Strange! I checked the SD card and I knocked the write protect switch when inserting it! Retried, and this time I got resource busy. That means OSX is using the disk, so I unmounted it (distinct from ejecting):

diskutil unmountDisk /dev/disk1

Then retry the dd. Success! To get status updates, use ctrl-T to send SIGINFO

load: 2.04  cmd: dd 30918 uninterruptible 0.00u 0.09s
192+0 records in
191+0 records out
200278016 bytes transferred in 18.724708 secs (10695922 bytes/sec)

This tells me i'm getting about 10mb/s. My class 6 SD card is rated for min 6m/s..

Took 3mins overall.
