## Linux-Unix - Partitioning

By Jack Szwergold, September 15, 2015

### Prerequisites

#### Install `lsblk`.

Pretty much all Linux systems have all of the necessary core tools available to detect, mount and format a block level device. But one tool called `lsblk` (list block devices) is not installed on some systems by default. This is a useful tool that presents a nice list of connected block devices and is visually easier to use than other methods of block device detection.

So to install it on CentOS/RedHat it can be installed via this command:

    sudo yum install util-linux-ng

And if you want to install it on Ubuntu/Debian it can be installed via this command:

    sudo apt-get install util-linux

Get that installed and you’re good to go.

#### Attach the device to the machine.

First, have the device attached to your server. If this is a physical machine, be sure it’s plugged in. If this is being attached via a virtual service like Amazon Web Service, make sure the new EBS storage volume is connected to the EC2 server instance.

### Quick Start

This is a quick distillation of the items gone into more detail in the “Detailed Explanation” area.

Now login to the machine and run `lsblk` to determine whick block level device you want to create or add a partition to:

    lsblk

Looking at the output of this, we spot a device named `xvdf` which is what we want to work with. Now check to see what the file type of the mount is; note how the `xvdf` value is now prepended by `/dev` which is the directory where these devices exist:

	sudo file -s /dev/xvdf

The output should be something like this:

    /dev/xvdf: data

Now we are going to run `fdisk` to partition the disk for usage with this command:

    sudo fdisk /dev/xvdf

You should see a prompt like this:

    Command (m for help):

You are now in the `fdisk` command area, which is good. Ignore any warnings or messages that precede that prompt; they are just saying the device has no partitions.

Now, in the `fdisk` command area hit `n` and press `enter/return`. The output after entering `n` will be this:

	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended

We will be creating a primary partition, so choose `p` and hit `return/enter`. The output after that would be:

    Partition number (1-4, default 1):

Since there are no existing partitions, we will choose `1` which will then show us the following info:

	First sector (2048-8388607, default 2048):

Just hit `return/enter` to accept the default of `2048` which will show us the following:

	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-8388607, default 8388607):

As you can see the default first sector is set to 2048. And now we are being asked for the last sector of the partition. It’s usually safe to accept the default, so just hit `return/enter`. The output would be:

    Using default value 8388607

Okay, now we have a partition created but it has not been saved to the disk. We can do that by hitting `w` and hit `return/enter`. The final output would be:

    The partition table has been altered!

    Calling ioctl() to re-read partition table.
    Syncing disks.

And you should be back at the main system command prompt and can test the device with `file` again:

    sudo file -s /dev/xvdf

Before partitioning, the info on `/dev/xvdf` showed it to be simply raw data. But after partitioning, it should be something like this indicating a partition is in place:

	/dev/xvdf: x86 boot sector; partition 1: ID=0x83, starthead 32, startsector 2048, 8386560 sectors, extended partition table (last)\011, code offset 0x0

And checking with `lsblk` should show the partion called `xvdf1` connected to the disk `xvdf`. Knowing the device is named `xvdf1` let’s format it as `ext3` like this:

    sudo mkfs -t ext3 /dev/xvdf1

After that is done, the device should be formatted and ready to mount. To mount a partition a diretory should exist on the system that would be a mount point. In this case we are creating a mountput in `/opt/` named `/opt/mount_test/`:

    sudo mkdir -p /opt/mount_test/

Now let’s manually mount the device to the mount point like this:

    sudo mount /dev/xvdf1 /opt/mount_test/

If the device mounted, you should be returned cleanly to the command prompt. To see if it is mounted, just check the output of `mount -l` or `df -h` to see it there.

And finally to make that volume mount automatically on system start, we need to edit the `fstab` like this:

    sudo nano /etc/fstab

And add this; note the device name of `/dev/xvdf1` as well as the mount point of `/opt/mount_test` in addition to the format type indicated as `ext3`:

    /dev/xvdf1    /opt/mount_test    ext3    defaults,nodev,nosuid,nobootwait    0    0

Now if the machine is stopped and restarted or simply rebooted, the volume will automatically be mounted without any additional work.

### Detailed Explanation

This is a more detailed explanation of the volume attaching, partitioning, mounting and setting auto-mounting process.

### Partitioning

#### Detect the attached block level device.

First, login to the machine you have attached the drive to and let’s see what partitions are attached to the server using `lsblk` command. Just type it in and hit `enter/return` like this:

    lsblk

And the output should be something like this:

	NAME                          MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda                             8:0    0    32G  0 disk
	├─sda1                          8:1    0   243M  0 part /boot
	├─sda2                          8:2    0     1K  0 part
	└─sda5                          8:5    0  31.8G  0 part
	  ├─sandbox--vg-root (dm-0)   252:0    0  27.8G  0 lvm  /
	  └─sandbox--vg-swap_1 (dm-1) 252:1    0     4G  0 lvm  [SWAP]
	xvdf                             8:16   0     4G  0 disk
	sr0                            11:0    1  1024M  0 rom

Or you can check via `/proc/partitions`:

	cat /proc/partitions

The list should look something like this:

	major minor  #blocks  name
	
	  11        0    1048575 sr0
	   8        0   33554432 sda
	   8        1     248832 sda1
	   8        2          1 sda2
	   8        5   33302528 sda5
	   8       16    4194304 xvdf
	 252        0   29106176 dm-0
	 252        1    4194304 dm-1

The way to decipher these lists is to look for the name that does not have any corresponding equivalent item with a number appended to it. That would be the newly attached device that has no partitions created. A quick galnce of the `lsblk` output shows there is a disk named `xvdf` with no partitions connected to it. That is what we need.

Now check to see what the file type of the mount is; note how the `xvdf` value is now prepended by `/dev` which is the directory where these devices exist:

	sudo file -s /dev/xvdf

The output should be something like this:

    /dev/xvdf: data

That means that the device has not been partitioned and is simply raw data. Great! Now let’s go and partition it.

#### Create a partition.

Knowing what block level device we are going to work on—the one named `xvdf`—let’s run `fdisk` via `sudo` on that device like this:

	sudo fdisk /dev/xvdf

The output would be something like this. Feel safe ignoring those warnings since they basically add up to, “Hey, this device has no partition.”:
	
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0xd3b5c150.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.
	
	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)
	
	Command (m for help):

Just for reference if you type in `m` you can get a menu of options:

	Command action
	   a   toggle a bootable flag
	   b   edit bsd disklabel
	   c   toggle the dos compatibility flag
	   d   delete a partition
	   l   list known partition types
	   m   print this menu
	   n   add a new partition
	   o   create a new empty DOS partition table
	   p   print the partition table
	   q   quit without saving changes
	   s   create a new empty Sun disklabel
	   t   change a partition's system id
	   u   change display/entry units
	   v   verify the partition table
	   w   write table to disk and exit
	   x   extra functionality (experts only)

For now, we will just create a new partition by choosing the `n` option and hitting `return/enter`. The output after entering `n` will be this:

	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended

We will be creating a primary partition, so choose `p` and hit `return/enter`. The output after that would be:

    Partition number (1-4, default 1):

Since there are no existing partitions, we will choose `1` which will then show us the following info:

	First sector (2048-8388607, default 2048):

Just hit `return/enter` to accept the default of `2048` which will show us the following:

	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-8388607, default 8388607):

As you can see the default first sector is set to 2048. And now we are being asked for the last sector of the partition. It’s usually safe to accept the default, so just hit `return/enter`. The output would be:

    Using default value 8388607

Okay, now we have a partition created but it has not been saved to the disk. We can do that by hitting `w` and hit `return/enter`. The final output would be:

    The partition table has been altered!

    Calling ioctl() to re-read partition table.
    Syncing disks.

And you should be back at the command prompt.

#### Confirm the partition has been created.

Now test the device with `file` again:

    sudo file -s /dev/xvdf

Before partitioning, the info on `/dev/xvdf` showed it to be simply raw data. But after partitioning, it should be something like this indicating a partition is in place:

	/dev/xvdf: x86 boot sector; partition 1: ID=0x83, starthead 32, startsector 2048, 8386560 sectors, extended partition table (last)\011, code offset 0x0

And checking with `lsblk` should show the partion called `xvdf1` connected to the disk `xvdf` like this:

	NAME                          MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda                             8:0    0    32G  0 disk
	├─sda1                          8:1    0   243M  0 part /boot
	├─sda2                          8:2    0     1K  0 part
	└─sda5                          8:5    0  31.8G  0 part
	  ├─sandbox--vg-root (dm-0)   252:0    0  27.8G  0 lvm  /
	  └─sandbox--vg-swap_1 (dm-1) 252:1    0     4G  0 lvm  [SWAP]
	xvdf                             8:16   0     4G  0 disk
	└─xvdf1                          8:17   0     4G  0 part
	sr0                            11:0    1  1024M  0 rom

To further confirm the partition has been created you can enter the following `fdisk` command to find the disk and partition info for the device `xvdf`:

    sudo fdisk -l | grep xvdf

The output should be something like this:

	Disk /dev/xvdf: 4294 MB, 4294967296 bytes
	/dev/xvdf1            2048     8388606     4193279+  83  Linux

The first line is the actual disk device named `xvdf`, but the second line is the partition we just created on that disk named `xvdf1`. Note that because next we’ll create a filesystem on the device.

### Filesystem & Mounting

#### Format a filesystem onto the partition.

Okay, attaching and partitioning a device is usually the hardest thing to do. If you have that done, let’s create a file system on the device.

Knowing the device is named `xvdf1` let’s format it as `ext3` like this:

    sudo mkfs -t ext3 /dev/xvdf1

The output should be something like this:

	mke2fs 1.42 (29-Nov-2011)
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	262144 inodes, 1048320 blocks
	52416 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1073741824
	32 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736
	
	Allocating group tables: done
	Writing inode tables: done
	Creating journal (16384 blocks): done
	Writing superblocks and filesystem accounting information: done

And as you can see, the last four items that return “done” are what we are looking for. If that all goes well, you now have a new partition formatted as an `ext3` volume. Now let’s create a mount point and mount that partition.

#### Create a mount point and mount the volume.

Okay, so to mount a partition a diretory should exist on the system that would be a mount point. In this case we are creating a mountput in `/opt/` named `/opt/mount_test/`:

    sudo mkdir -p /opt/mount_test/

Now let’s manually mount the device to the mount point like this:

    sudo mount /dev/xvdf1 /opt/mount_test/

If the device mounted, you should be returned cleanly to the command prompt.

#### Confirm the partition is mounted on the mount point.

To check if it is indeed mounted, run the `df` command to get an overview of filesystem disk space usage like this:

    df -h

The output would be something like this:

	Filesystem                    Size  Used Avail Use% Mounted on
	/dev/mapper/sandbox--vg-root   28G  6.0G   20G  24% /
	udev                          2.0G  4.0K  2.0G   1% /dev
	tmpfs                         396M  332K  395M   1% /run
	none                          5.0M     0  5.0M   0% /run/lock
	none                          2.0G     0  2.0G   0% /run/shm
	/dev/sda1                     236M   33M  191M  15% /boot
	/dev/xvdf1                     3.9G  8.1M  3.7G   1% /opt/mount_test

As you can see at the bottom of the list `xvdf1` is mounted on `/opt/mount_test`.

You can also check what volumes are mounted running the `mount -l` command like this:

    mount -l

And output would be something like this:

	/dev/mapper/sandbox--vg-root on / type ext4 (rw,errors=remount-ro)
	proc on /proc type proc (rw,noexec,nosuid,nodev)
	sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
	none on /sys/fs/fuse/connections type fusectl (rw)
	none on /sys/kernel/debug type debugfs (rw)
	none on /sys/kernel/security type securityfs (rw)
	udev on /dev type devtmpfs (rw,mode=0755)
	devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=0620)
	tmpfs on /run type tmpfs (rw,noexec,nosuid,size=10%,mode=0755)
	none on /run/lock type tmpfs (rw,noexec,nosuid,nodev,size=5242880)
	none on /run/shm type tmpfs (rw,nosuid,nodev)
	/dev/sda1 on /boot type ext2 (rw)
	rpc_pipefs on /run/rpc_pipefs type rpc_pipefs (rw)
	/dev/xvdf1 on /opt/mount_test type ext3 (rw)

As before, as you can see at the bottom of the list `xvdf1` is mounted on `/opt/mount_test`.

And you can further check the contents of the volume with `ls` like this:

    ls -lah /opt/mount_test

And the output should be something like this:

	total 24K
	drwxr-xr-x 3 root root 4.0K Jan 21 18:51 .
	drwxr-xr-x 3 root root 4.0K Jan 21 18:59 ..
	drwx------ 2 root root  16K Jan 21 18:51 lost+found

And now the space is ready to use!

If you need to manually unmount it just run this command:

    sudo umount /opt/mount_test/

### Automatic Mounting & Resizing

#### Automatically mounting a volume on boot.

If you want to make this device mount automatlly on reboot, you need to add an `fstab` entry. First, check what volumes are mounted running the `mount -l` command like this:

    mount -l

Output should contain the following:

    /dev/xvdf1 on /opt/mount_test type ext3 (rw)

Make note of the device partition (`/dev/xvdf1`) and the related mount point (`/opt/mount_test`). So now, to make that volume mount automatically on system start, we need to edit the `fstab` like this:

    sudo nano /etc/fstab

And add this:

    /dev/xvdf1    /opt/mount_test    ext3    defaults,nodev,nosuid,nobootwait    0    0

#### Resizing a volume.

Sometimes if you resize a volume—increase the size or decrease the size—chances are the partition inside of it won’t expand to fill up the space. This is a fairly easy issue to solve by using `resize2fs`.

First, a task like this should not be done on a live system so be sure to unmount the volume like this:

    sudo umount /opt/mount_test/

Force check the volume like this:

    sudo e2fsck -f /dev/xvdf1

The output of that command should be something like this:

	e2fsck 1.42 (29-Nov-2011)
	Pass 1: Checking inodes, blocks, and sizes
	Pass 2: Checking directory structure
	Pass 3: Checking directory connectivity
	Pass 4: Checking reference counts
	Pass 5: Checking group summary information
	/dev/xvdf1: 11/131072 files (0.0% non-contiguous), 25388/524288 blocks

Now run `resize2fs`:

    sudo resize2fs /dev/xvdf1

If the volume is already properly sized, it will spit out something like this:

	resize2fs 1.42 (29-Nov-2011)
	The filesystem is already 1048319 blocks long.  Nothing to do!

If the volume has to adjust itself to deal with increased or decreased space, the output would be:

	resize2fs 1.42 (29-Nov-2011)
	Resizing the filesystem on /dev/xvdf1 to 1048320 (4k) blocks.
	The filesystem on /dev/xvdf1 is now 1048320 blocks long.

When you remount the volume—either manually or via the reboot—the size of the available volume should be increased or decreased depending on what type of resizing was done.

***

*Linux-Unix - Partitioning (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
