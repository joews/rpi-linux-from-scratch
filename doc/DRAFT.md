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

## Installing host system dependencies

`version_check.sh` (http://www.linuxfromscratch.org/lfs/view/stable/prologue/hostreqs.html) shows that several dependencies are missing. To install on Arch:

pacman -Sy
pacman -S make gcc patch bison

(It is possible to pacman -S base-devel, but I wanted to keep disk usage to a minimum)

I foun I was still missing /usr/lib/libgmp.la, /usr/lib/libmpfr.la and /usr/lib/libmpc.la. I couldn't find packages for these fies (using the handy `pkgfile` utility) so I decided to press ahead without them, as suggested by the LFS docs (either all 3 or 0 should be there).

My arch partition used 589mb (df --si)

[root@alarmpi ~]# df --si
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       1.8G  589M  1.1G  37% /
devtmpfs         88M     0   88M   0% /dev
tmpfs           243M     0  243M   0% /dev/shm
tmpfs           243M  291k  243M   1% /run
tmpfs           243M     0  243M   0% /sys/fs/cgroup
tmpfs           243M     0  243M   0% /tmp
/dev/mmcblk0p1   95M   26M   69M  28% /boot
tmpfs            49M     0   49M   0% /run/user/0


I wanted to shrink the root partition to make more room for LFS:
https://gist.github.com/jwhitfieldseed/10671144

Then I did all the installing of gcc, etc

Once I had done this, and made my new partition:

mkfs.ext4 /dev/mmcblk0p6
mkdir -p /mnt/lfs
mount -O suid,dev /dev/mmcblk0p6 /mnt/lfs/


Followed instructions. Learned about target triples, static/dynamic linkers and how to cross-compile a toolchain.

First build, binutils, is the benchmark. It took 50 mins. Next is GCC, which is expected to take 5 times longer.
