# Cheat Sheet - Linux-Unix - Various LVM Related Items

By Jack Szwergold, September 30, 2015

#### Renaming an LVM logical volume group.

Show all the volume groups on the system:

	sudo vgdisplay

Rname the volume group:

	sudo vgrename [old name] [new name]

Double check the volume groups again to make sure the change went through:

    sudo vgdisplay

Edit the `fstab` to reflect the logical volume name change. Note that if the volume group name has a `-` in it, it needs to be doubled in the `fstab`. So a volume group named `sandbox-vg` would need to be `sandbox--vg` in the `fstab`:

	sudo nano /etc/fstab

Update the GRUB bootloader to get the new logical volume name recognized:

	sudo update-grub2

The update the existing `initramfs` stuff like this:

	sudo update-initramfs -u

### Creating a new LVM logical volume group.

#### Creating the physical volume.

Use `lsblk` to see a list of all connected block level devices; in this example we are acting on `/dev/sdb`:

    lsblk

Create the physical volume for the new LVM like this:

    sudo pvcreate /dev/sdb

Next run `pvscan` to get a short list of physical volumes connected to the the machine:

    sudo pvscan

The output should be something like this:

	PV /dev/sda5   VG sandbox-vg      lvm2 [31.76 GiB / 0    free]
	PV /dev/sdb                       lvm2 [8.00 GiB]
	Total: 2 [39.76 GiB] / in use: 1 [31.76 GiB] / in no VG: 1 [8.00 GiB]

Or run `pvdisplay` to get deeper information on the physical volumes:

    sudo pvdisplay

Now let’s create a new logical volume group called `test_group` like this:

    sudo vgcreate test_group /dev/sdb

The response from that should be something like this:

    Volume group "test_group" successfully created

And if you run `pvscan` again the output should be something like this:

    PV /dev/sdb    VG test_group   lvm2 [8.00 GiB / 8.00 GiB free]
    PV /dev/sda5   VG sandbox-vg   lvm2 [31.76 GiB / 0    free]
    Total: 2 [39.75 GiB] / in use: 2 [39.75 GiB] / in no VG: 0 [0   ]

#### Creating the logical volume.

Now let’s create the logical volume like so:

    sudo lvcreate -L 8G -n test_volume test_group

If that command fails with an error like this:

    Insufficient free extents (2047) in volume group test_group: 2048 required

Try running this command:

    sudo lvcreate -l2047 -n test_volume test_group

Check the list of logical volumes like so:

    sudo lvscan

The list should look something like this:

    ACTIVE            '/dev/test_group/test_volume' [8.00 GiB] inherit
    ACTIVE            '/dev/sandbox-vg/root' [27.76 GiB] inherit
    ACTIVE            '/dev/sandbox-vg/swap_1' [4.00 GiB] inherit

You can remove a logical volume like so:

    sudo lvremove /dev/test_group/test_volume

#### Creating file system and mounting.

Knowing the device is located at `/dev/test_group/test_volume` let’s format it as `ext3` like this:

    sudo mkfs -t ext3 /dev/test_group/test_volume

After that is done, the device should be formatted and ready to mount. Let’s create a test mount poin called `mount_test`:

    sudo mkdir -p ~/mount_test/

Now let’s manually mount the device to the mount point like this:

    sudo mount /dev/test_group/test_volume ~/mount_test/

If the device mounted, you should be returned cleanly to the command prompt. To see if it is mounted, just check the output of `mount -l` or `df -h` to see it there.

And finally to make that volume mount automatically on system start, we need to edit the `fstab` like this:

    sudo nano /etc/fstab

And add this; note the device name of `/dev/test_group/test_volume` as well as the mount point of `/home/sysop/mount_test` in addition to the format type indicated as `ext3`:

    /dev/test_group/test_volume    /home/sysop/mount_test    ext3    defaults,nodev,nosuid,nobootwait    0    0

Now if the machine is stopped and restarted or simply rebooted, the volume will automatically be mounted without any additional work.

#### Adding a disk to an LVM logical volume group.

Use `lsblk` to see a list of all connected block level devices; in this example we are acting on `/dev/sdb`:

    lsblk

Run `cfdisk` to partition the disk:

	sudo cfdisk /dev/sdb

Then use `pvcreate` initialize the partition for use by LVM:

	sudo pvcreate /dev/sdb1

Response should be something like this:

    Physical volume "/dev/sdb1" successfully created

Now get your volume group name with `vgdisplay` like this:

    sudo vgdisplay

In this case let’s assume the volume group name is `sandbox-vg`. So now let’s add it to the existing volume group using `vgextend` like this:

	sudo vgextend sandbox-vg /dev/sdb1

The response should be something like this:

	Volume group "sandbox-vg" successfully extended


and now we can finally extend my logical volume using :

	lvextend -L+20G /dev/zabbix/root 

And if now check fdisk -l there is /zabbix-root with +20GB But dont forget to use `resize2fs` to let filesystem know there is some change. If you will not do this you are not able to use new space.

	resize2fs /dev/zabbix/root

Next steps shgould be something like this, but it doesn’t seem to be working:

    sudo lvextend -L+8G /dev/mapper/sandbox--vg-root

    sudo resize2fs /dev/mapper/sandbox--vg-root

#### Sundry LVM debugging stuff.

Check the current UUID:

	sudo blkid -c /dev/null

***

*Cheat Sheet - Linux-Unix - Various LVM Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

