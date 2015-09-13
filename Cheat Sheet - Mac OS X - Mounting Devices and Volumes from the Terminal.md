# Cheat Sheet - Mac OS X - Mounting Devices and Volumes from the Terminal

By Jack Szwergold, September 12, 2015

#### Mounting disk images that throw a “no mountable filesystems” error. 

Some disk images—such as those created of system disks by “Disk Utility”—might throw up a “no mountable filesystems” error if there is an attempt to mount the disk image directly in the Finder. The instructions below allow you to mount the disk via the command line in the terminal.

First, we need to attach the disk to the system by running this command:

	hdiutil attach -noverify -nomount disk_image.dmg

Once that command is run check the list of available disks via `diskutil` like this:

	diskutil list

Look through that list and find the device ID for your the connected image; it should be the highest number near the bottom of the list. The device ID should look something like `disk[device id]` where `[number]` is device ID number itself.

Once you get the device ID, you can mount the device like this; change `disk[device id]` to be the actual device ID number you just figured out in the last step:

    diskutil mountDisk /dev/disk[device id]

Note that with that command you are mounting a whole disk instead of a partition which would be something like `disk[device id]s[partition id]`. if somehow you are dealing with an unmountable partition, just us the `disk[device id]s[partition id]` in the examples above.

#### Remounting an ejected USB flash drive.

Let’s say somehow you ejected a USB flash drive you need to to use and for whatever reason—you are remoted into a remote machine or are just lazy—you simply can’t remount it by pulling the USB flash drive in an out again. This is what you can do to get the USB flash drive mounted again via the command line.

First—with the USB flash drive ejected—run this `kextunload` command to unload `IOUSBMassStorageClass.kext`:

    sudo kextunload /System/Library/Extensions/IOUSBMassStorageClass.kext

Then run this `kextload` command to reload the `IOUSBMassStorageClass.kext`:

    sudo kextload /System/Library/Extensions/IOUSBMassStorageClass.kext

The USB drive should now be remounted and accessible again.

***

*Cheat Sheet - Mac OS X - Mounting Devices and Volumes from the Terminal (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*