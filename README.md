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
 
## Replacing squashfs   
 
```sf probe
sf erase 0x1d0000 0x5E0000 
setenv serverip 192.168.3.1
dhcp rootfs.sq
sf write 0x20006000 0x1d0000 0x5be000
```
