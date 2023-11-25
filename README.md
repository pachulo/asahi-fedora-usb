Creates a Fedora usb drive that will boot on Apple M1/M2 systems.

## Fedora Package Install
```dnf install arch-install-scripts bubblewrap gdisk mkosi qemu-user-static pandoc rsync systemd-container```

### Notes

- ```qemu-user-static``` is only needed if building the image on a ```non-aarch64``` system  
- If building the image on a **Fedora 38** system install mkosi v18:  
  `python3 -m pip install --user git+https://github.com/systemd/mkosi.git@v18`
- If building the image on a **Fedora 39** system you need to install mkosi from main:  
  `python3 -m pip install --user git+https://github.com/systemd/mkosi.git`


To build a minimal Fedora image and install it to a usb drive, simply run:
```
./build.sh -d /dev/sda
```

**note:** substitute ```/dev/sda``` with the device id of your usb drive

If you've previously installed this Fedora image to the usb drive, you can wipe the drive and install a new image without having to repartition/reformat the drive by providing the `-w` argument
```
./build.sh -wd /dev/sda
```

Once the drive is created, you can locally mount, unmount, or chroot into the usb drive (which contains 3 partitions) to/from ```mnt_usb/``` with
```
./build.sh mount
./build.sh umount
./build.sh chroot
```
**note:** mounting the usb drive is useful for inspecting the contents of the drive or making changes to it

To boot the usb drive on an apple silicon system, you could either:
1. Enter the following ```u-boot``` commands at boot time:
```
env set boot_efi_bootmgr
run usb_boot
```
2. Add the usb drive to the grub menu on the internal drive  
You just need to boot Linux on the **internal drive**, plugin the usb drive, and then update grub
```
[root@m1 ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found Fedora Linux Asahi Remix 38 (Thirty Eight) on /dev/nvme0n1p6
Found Fedora Linux Asahi Remix 38 (Thirty Eight) on /dev/sda3
done
```
You should now see the `/dev/sda3` entry in the main grub menu  
If you don't see the grub menu at all or if the text is garbled, then ensure these options are set in `/etc/default/grub`
and then run `grub2-mkconfig -o /boot/grub2/grub.cfg` on the internal drive
```
GRUB_TIMEOUT=5
GRUB_TIMEOUT_STYLE="menu"
GRUB_TERMINAL_OUTPUT=""
```

**Setting up WiFi**

To connect to a wireless network, use the following sytanx:
```nmcli dev wifi connect network-ssid```

An actual example:
```nmcli dev wifi connect blacknet-ac password supersecretpassword```

**Rescuing a Fedora install**

Two helper scripts have been added to this image  
Which are useful if you have Fedora installed on the internal drive:
```
/usr/local/sbin/chroot.asahi
/usr/local/sbin/umount.asahi
```
1. `chroot.asahi` will mount the (Fedora) internal drive under `/mnt` and will `arch-chroot` into it.  
To exit from the `chroot` environment, simply type `ctrl+d` or `exit`

2. `umount.asahi` will unmount the internal drive from `/mnt`
