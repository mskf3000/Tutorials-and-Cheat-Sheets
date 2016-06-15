## Linux-Unix - exFAT Partitioning and Formatting

By Jack Szwergold

This document explains the basics of partitioning, formatting, mounting and labelling a block level device from the command line in Linux/Unix.

### Check for block level devices.

Both `lsblk` and `fdisk` can provide details on devices connected to your system, but `lsblk` is a bit nicer and easier to visually understand. Use `fdisk` for something like this if `lsblk` is not installed.

Use `lsblk` to see a list of all connected block level devices:

    lsblk

Use `fdisk` to see a list of all connected and mounted devices:

    sudo fdisk -l

Once you get the device identifier for the device just make note of it for the rest of your device operations. For this example, we’ll be using `/dev/sdb`.

### Partition a device using `fdisk`.

Knowing you will be working on `/dev/sdb`, run `fdisk` so it can partition `/dev/sdb` like this:

    sudo fdisk /dev/sdb

Type `d` to delete a partition. It should say something like:

    Selected partition 1

Type `n` to create a new partition and select `p` for `primary` partition:

	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended

After doing that, just punch through the rest of the sundry specifics like this. Trust the prefilled values unless you know what you are doing explictly need to use different value:

	Partition number (1-4, default 1):
	Using default value 1
	First sector (2048-30801919, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-30801919, default 30801919):
	Using default value 30801919

Type `t` to set the partition type and then select `7` which is `HPFS/NTFS/exFAT` partition type:

	Command (m for help): t
	Selected partition 1
	Hex code (type L to list codes): 7
	Changed system type of partition 1 to 7 (HPFS/NTFS/exFAT)

Check the selected partition type by typing `p` to make sure all is good. The output should say something like this:

	Disk /dev/sdb: 15.8 GB, 15770583040 bytes
	85 heads, 23 sectors/track, 15755 cylinders, total 30801920 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x00000000
	
	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdb1            2048    30801919    15399936    7  HPFS/NTFS/exFAT

Now type `w` to write the partition data to the disk and exit `fdisk`. The output should be like this:

	The partition table has been altered!
	
	Calling ioctl() to re-read partition table.
	Syncing disks.

With partitioning of the device done, the next step of the process involves formatting the device with an actual file system.

### Format a device’s partition using `mkfs`.

#### Format the device as exFAT.

Install the exFAT related items onto your system like this:

    sudo aptitude install exfat-utils exfat-fuse

Now format the partition as exFAT:

    sudo mkfs.exfat -n FooBar /dev/sdb1

If all goes well, the output for that would be something like this:

	mkexfatfs 1.0.1
	Creating... done.
	Flushing... done.
	File system created successfully.

And you are all done! The partition located at `/dev/sdb1` has been successfully formatted as an exFAT device with the label/name being set to “FooBar.”

#### Format the device as HFS.

Install the HFS related Linux tools onto your system like this:

    sudo aptitude install hfsutils

Now format the partition as HFS:

    sudo hformat -l FooBar /dev/sdb1

If all goes well, the output for that would be something like this:

	Volume name is "FooBar"
	Volume was created on Mon Nov 10 00:13:08 2014
	Volume was last modified on Mon Nov 10 00:13:08 2014
	Volume has 15523445760 bytes free

### Mounting a device’s partition using `mount`.

The first thing you need to do before mounting a volume is to create a target directory. In this case, we’ll create one named  `usb_mount` and place it in your user’s home directory:

    mkdir -p ~/usb_mount

With that directory set, mount the volume using `mount` like this:

    sudo mount /dev/sdb1 ~/usb_mount

Once that is done, if you do a `df -h` you should be able to see the volume mounted like this:

	Filesystem                    Size  Used Avail Use% Mounted on
	/dev/mapper/sandbox--vg-root  6.5G  4.3G  1.9G  70% /
	udev                          487M  4.0K  487M   1% /dev
	tmpfs                         100M  344K  100M   1% /run
	none                          5.0M     0  5.0M   0% /run/lock
	none                          497M     0  497M   0% /run/shm
	/dev/sda1                     228M   34M  183M  16% /boot
	/dev/sdb1                      15G  2.1M   15G   1% /home/sysop/usb_mount

Now the storage space on that volume is accessible to the OS and you can interact with that mounted file system.

### Unmounting a device’s partition using `mount`.

If you wish to unmount the volume, just run the `umount` command like this:

    sudo umount ~/usb_mount

And if you want to decouple the device from the system completely, it can be ejected via `eject` like this:

    sudo eject /dev/sdb1

After ejecting you can physically connect the drive from the system.

### Sundry device label related items.

Depending on the OS you are on, labelling of volumes might not be an issue. In command line Linux, you might never run across the volume label. But when interacting with the volume in Mac OS X—for example—the label is the name the volume will be mounted on for you to use in the Finder.

Run this command to list all the block device attributes including the device’s label:

    sudo blkid
   
Run this command to check the device label on an ExFAT formatted volume:

    sudo exfatlabel /dev/sdb1

Run this command to change the device label on an ExFAT formatted volume:

    sudo exfatlabel /dev/sdb1 FooBar

***

*Linux-Unix - exFAT Partitioning and Formatting (c) by Jack Szwergold; written September 11, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*