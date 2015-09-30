# Cheat Sheet - Linux-Unix - Various LVM Related Items

By Jack Szwergold, September 30, 2015

#### Renaming an LVM logical volume.

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

#### Adding a disk to an LVM logical volume.

Use `lsblk` to see a list of all connected block level devices:

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

