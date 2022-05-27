Creates a Fedora usb drive that will boot on Apple M1 systems.

To build a minimal Fedora image and install it to a usb drive, simply run:
```
./build.sh -d /dev/sda
```

**substitute ```/dev/sda``` with the device id of your usb drive

Upon completion, you can mount and unmount the usb drive (which contains 3 partitions) to/from ```mnt_usb/``` with 
```
./build.sh mount
./build.sh umount
```

To boot the usb drive on an M1 system, enter the following ```u-boot``` command at boot time:
```
run bootcmd_usb0
```

The usb drive is read/write and contains three partitions: ```efi, boot, and root``` (just like a normal system)  
Also ```mkosi``` has cross-architecture support, so you build this on an x86_64 system no problem.  
\*\*I'll provide more documentation later...
