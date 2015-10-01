# Cheat Sheet - Linux-Unix - GPT (GUID Partition Table) Related Items

By Jack Szwergold, September 30, 2015

### Getting rid of a GPT/GUID disk label.

#### What’s this all about?

This would happen if you use a GPT/GUID-centric system like Mac OS X, format a disk and then need to use it on a Linix system that doesn’t support GPT/GUID stuff. For example, I formatted a USB flash drive in Mac OS X, connected it to an Ubuntu Linux system and ran `fdisk` to list the partition tables like this:

    sudo fdisk -l /dev/sdb

And the output returned was like this:

	WARNING: GPT (GUID Partition Table) detected on '/dev/sdb'! The util fdisk doesn't support GPT. Use GNU Parted.
	
	
	Disk /dev/sdb: 8019 MB, 8019509248 bytes
	255 heads, 63 sectors/track, 974 cylinders, total 15663104 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x00000000
	
	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdb1               1    15663103     7831551+  ee  GPT

Okay, that setup might be useful for you in Mac OS X, but not in a pure Linux system. So this is what we can do to wipe away that partition table so we’re working with a clean device that won’t throw up any warnings.

#### How to wipe out the GPT/GUID disk label.

First run `sfdisk` to get some basic info on the device itself:

    sudo sfdisk -g /dev/sdb

The output should be something like this:

	WARNING: GPT (GUID Partition Table) detected on '/dev/sdb'! The util sfdisk doesn't support GPT. Use GNU Parted.
	
	/dev/sdb: 1022 cylinders, 247 heads, 62 sectors/track

Knowing that, let’s multiply the cylinders, heads and sectors using `bc` (basic calculator) to get a “seek” value that points to an area near the end of the device with an offset 18 blocks:

    echo 1022*247*62-18 | bc

The returned value would be `15650890`. Make note of that for the step after the this one. For this step let’s wipe the partition info from the beginning of the device to about 18 blocks in:

    sudo dd if=/dev/zero of=/dev/sdb bs=512 count=18

Output should be something like this:

	18+0 records in
	18+0 records out
	9216 bytes (9.2 kB) copied, 0.030941 s, 298 kB/s

Now run this next command to wipe the partition info near the end of the device using the “seek” value of `15650890`:

    sudo dd if=/dev/zero of=/dev/sdb bs=512 seek=15650890

And the output for that should be something like this:

	dd: writing `/dev/sdb': No space left on device
	12215+0 records in
	12214+0 records out
	6253568 bytes (6.3 MB) copied, 14.6603 s, 427 kB/s

When that is all done, run `sfdisk` to check the basic info on the device again:

    sudo sfdisk -g /dev/sdb

And the output should be a clean report of device details without GPT/GUI a warning:

    /dev/sdb: 1022 cylinders, 247 heads, 62 sectors/track

***

*Cheat Sheet - Linux-Unix - GPT (GUID Partition Table) Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*