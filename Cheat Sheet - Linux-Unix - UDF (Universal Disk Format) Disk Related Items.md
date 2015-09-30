# Cheat Sheet - Linux-Unix - UDF (Universal Disk Format) Disk Related Items

By Jack Szwergold, September 30, 2015

Use `lsblk` to see a list of all connected block level devices:

    lsblk

Knowing we will be using `/dev/sdb`, partition the USB drive; mainly to wipe away all partitions:

	sudo fdisk /dev/sdb

	d
	1
	n
	p
	1
	first sector
	last sector
	t
	1
	6: Changed system type of partition 1 to 6 (FAT16)
    w
	
***

	sudo dd if=~/ubuntu-12.04.5-server-amd64.iso of=/dev/sdb bs=1024
	
***

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