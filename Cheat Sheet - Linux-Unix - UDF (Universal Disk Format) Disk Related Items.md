# Cheat Sheet - Linux-Unix - UDF (Universal Disk Format) Disk Related Items

By Jack Szwergold, September 30, 2015

#### Prepping and partitioning the device for use.

First, use `lsblk` to see a list of all connected block level devices:

    lsblk

In this case we’ll be acting on the device `/dev/sdb` so this will help you check exactly what `/dev/sdb` is:

    sudo file -s /dev/sdb

That output would be something like this:

    /dev/sdb: data

Knowing that the device is accessible and ready, we will partition the device like this:

	sudo fdisk /dev/sdb

Once in the `fdisk` interface, press `n` for a new partition. The output will be something like this:

	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended

Select `p` for a `primary` partition. Select all of the defaults for the items like partition number, first sector and last sector.

When that is done hit `t` for the partition type. The new prompt would be:

    Hex code (type L to list codes):

If it asks you to select a partition to act on, rememeber the partition number from above and enter that; it should be `1`. Then select `6` for the `FAT16` partition type. The output should then be:

    Changed system type of partition 1 to 6 (FAT16)

With that done type `w` to write the partition info to the disk and the process of partitioning should be finished and you will see the following output:

	The partition table has been altered!
	
	Calling ioctl() to re-read partition table.
	
	WARNING: If you have created or modified any DOS 6.x
	partitions, please see the fdisk manual page for additional
	information.
	Syncing disks.

Don’t worry about the warning. You’re all done as far as partitioning goes.
	
#### Prepping and partitioning the device for use.

	sudo dd if=~/ubuntu-12.04.5-server-amd64.iso of=/dev/sdb bs=1024
	
***

After the process:

    sudo file -s /dev/sdb

    /dev/sdb: # ISO 9660 CD-ROM filesystem data 'Ubuntu-Server 12.04.5 LTS amd64 ' (bootable)

### Sundry UDF and similar items.

First make sure the `udftools` are installed on your system.

	sudo aptitude install udftools udisks

Get info on the ISO itself with a plain `file` check like this:

    file ubuntu-12.04.5-server-amd64.iso

Output would be something like this:

    ubuntu-12.04.5-server-amd64.iso: # ISO 9660 CD-ROM filesystem data 'Ubuntu-Server 12.04.5 LTS amd64 ' (bootable)

Check the info on a “special file”—such as a block level device—by using `file` with the `-s` option:

    sudo file -s /dev/sda

The output of that would be something like this:

	/dev/sda: x86 boot sector; partition 1: ID=0x83, active, starthead 32, startsector 2048, 497664 sectors; partition 2: ID=0x5, starthead 59, startsector 501758, 66605058 sectors, code offset 0x63

***

	sudo mkudffs --media-type=hd --blocksize=512 /dev/sdb1

***

	mkdir -p ~/ubuntu-12.04.5
	
	sudo mount -o ro,loop ubuntu-12.04.5-server-amd64.iso ~/ubuntu-12.04.5
	
	sudo umount ~/ubuntu-12.04.5

***

*Cheat Sheet - Linux-Unix - UDF (Universal Disk Format) Disk Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*