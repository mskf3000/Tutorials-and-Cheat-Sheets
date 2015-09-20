# Cheat Sheet - Linux-Unix - MSDOS Floppy Disk Reading, Formatting and Partitioning

By Jack Szwergold, September 18, 2015

#### An adjustment to the GRUB bootloader that needs to be made to allow the floppy to be recognized.

Edit the default GRUB config file:

    sudo nano /etc/default/grub

Change this line:

    GRUB_CMDLINE_LINUX_DEFAULT=""

To this:

    # GRUB_CMDLINE_LINUX_DEFAULT=""
    GRUB_CMDLINE_LINUX_DEFAULT="floppy=no_acpi"

Now update GRUB and reboot:

    sudo update-grub2

#### Installing the MSDOS and floppy drive related software packages.

Install `mtools`, `dosfstools` and `ufiformat` via `aptitude` like this:

    sudo aptitude install mtools dosfstools ufiformat

What each of those things are:

- **mtools:** Utilities to access DOS disks in Unix.
- **dosfstools:** Utilities for making and checking MS-DOS FAT filesystems
- **ufiformat:** Format a USB floppy disk.

#### Mount the system on the floppy disk.

Show a list of all connected block level devices using `lsblk` and make note of the floppy disk:

    lsblk

Output should be something like this. The floppy should be near the end of the list and be a disk without any related partitions connected to it:

	NAME                          MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda                             8:0    0    32G  0 disk
	├─sda1                          8:1    0   243M  0 part /boot
	├─sda2                          8:2    0     1K  0 part
	└─sda5                          8:5    0  31.8G  0 part
	  ├─sandbox--vg-root (dm-0)   252:0    0  27.8G  0 lvm  /
	  └─sandbox--vg-swap_1 (dm-1) 252:1    0     4G  0 lvm  [SWAP]
	sdb                             8:16   1   1.4M  0 disk
	sr0                            11:0    1  1024M  0 rom

In this case it’s `sdb` so the full device path should be `/dev/sdb`.

Now, let’s create a mount point like this in your home directory:

    mkdir -p ~/usb_mount

Using the full device path noted earlier (`/dev/sdb`) we would mount it like this:

    sudo mount /dev/sdb ~/usb_mount

After the disk is mounted you can confirm it’s mounted and available by running `df -h` like this:

    df -h

The output should be something like this; note the `/dev/sdb` mounted with 1.4M of available space on it:

	Filesystem                    Size  Used Avail Use% Mounted on
	/dev/mapper/sandbox--vg-root   28G  5.4G   21G  21% /
	udev                          2.0G  8.0K  2.0G   1% /dev
	tmpfs                         396M  352K  395M   1% /run
	none                          5.0M     0  5.0M   0% /run/lock
	none                          2.0G     0  2.0G   0% /run/shm
	/dev/sda1                     236M   36M  188M  16% /boot
	/dev/sdb                      1.4M     0  1.4M   0% /home/sysop/usb_mount

Check out what’s on that disk by running `ls` like this:

    ls -la ~/usb_mount

When you are done with the floppy disk it can be unmounted like this:

    sudo umount ~/usb_mount

And it can be ejected like this:

    sudo eject /dev/sdb

#### Formatting a floppy disk.

Use `ufiformat` to actually format the floppy disk:

    sudo ufiformat -v -f 1440 /dev/sdb

Use `mkdosfs` to set the file system with a partition:

    sudo mkdosfs -v -I -n Untitled /dev/sdb

Use `mkfs.vfat` to set the file system as bootable:

    sudo mkfs.vfat -v -I -n Untitled /dev/sdb

#### Some sundry block level device and debugging items.

Shows a list of all connected block level devices using `lsblk`:

    lsblk

Shows all connected & mounted devices with `fdisk`:

    sudo fdisk -l

List all USB devices with the `-v` verbose flag:

    lsusb -v

Run this command to list all the block level ID info:

    sudo blkid

Check if the ‘floppy’ module is loaded:

    lsmod | grep -i floppy

If the ‘floppy’ module is not loaded, then manually add it:

    sudo modprobe -v floppy

Check if the kernel itself has floppy support:

    grep CONFIG_BLK_DEV_FD /boot/config-$(uname -r)

Check kernel messages related to the floppy disk:

    dmesg | grep floppy

Check the kernel module info on the floppy disk:

    modinfo floppy

Monitor devices being removed or added to a system via `udev`:

    udevadm monitor --udev

Check the `dmesg` output while a device is being removed or added to a system:

    sudo dmesg

Watch the `dmesg` output in realtime with `watch`: 

    watch -n 0.1 "dmesg | tail -n $((LINES-6))"

Check out attached hardware:

    sudo lshw

Count the number of connected devices:

    ls -latr /dev/* | wc -l

Print USB device details:

    usb-devices

***

*Cheat Sheet - Linux-Unix - MSDOS Floppy Disk Reading, Formatting and Partitioning (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
