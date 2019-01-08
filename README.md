# Wha?

Hacks for the MC200C2 module available from Banggood:
https://www.banggood.com/H_265-Mstar-2-Million-Starlight-Network-Module-Low-Bit-Rate-Monitoring-IP-Chip-With-Camera-p-1293598.html

## Flash Layout
 
```0x000000020000-0x00000003f000 : "UBOOT"
0x000000040000-0x0000001d0000 : "KERNEL"
0x0000001d0000-0x0000007b0000 : "SYSTEM"
0x000000003000-0x000000004000 : "MXPT"
0x0000007b0000-0x000000800000 : "CFG"
``` 

## Boot process
 
Kernel partition contains a kernel + initramfs combo image.
Initramfs contains some sort of hacked up Android init.
Squashfs is then mounted to /system

The root password is set to a random value on each
boot by the main controller process so really you need to 
replace the rootfs to get access.

## Moving parts

Once the camera has booted and mounted the squashfs it starts
up a ton of crap. (TODO: fill in exact process names etc).

There are a bunch of "something_server" processes that handle
different parts of the operation and they all seem to
communicate via a message queue or a socket with some sort of
main controller process that is responsible for parsing the
configuration and some weird sort of copy protection scheme
that reads a file stored on a JFFS2 filesystem system in the
CFG partition and does some magic to work out if the camera
module is legit or not. Maybe there is some efuse or something
in the SoC that it's comparing it with?

Ignoring all of that junk the actual camera and H264/H265
stuff seems fairly simple. There are a bunch of vendor
supplied OpenMAX libraries (one for each hardware block)
that should be usable with gstreamers gst-omx plugin.

## Replacing the squashfs
 
```sf probe
sf erase 0x1d0000 0x5E0000 
setenv serverip 192.168.3.1
dhcp rootfs.sq
sf write 0x20006000 0x1d0000 0x5be000
```

## Uboot and kernel sources

https://github.com/fifteenhex/uboot_msc313e
https://github.com/fifteenhex/linux_msc313e
