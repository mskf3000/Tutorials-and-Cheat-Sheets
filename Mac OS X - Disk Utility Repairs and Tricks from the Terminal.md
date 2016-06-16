## Mac OS X - Disk Utility Repairs and Tricks from the Terminal

By Jack Szwergold

### Using `fsck_hfs` to repair a volume that “Disk Utility” chokes on.

Apple’s GUI application “Disk Utility” works fine for sundry hard disk issues, but sometimes it just chokes on drives claiming it can’t repair the disk. Before you give up hope, you can see if using `fsck_hfs` will

First, check the list of available disks via `diskutil` like this:

    diskutil list

Look through that list and find the device ID of the volume you plan on repairing. The device ID should look something like `disk[device id]s[partition id]` where `[number]` is device ID number itself and `[partition id]` is the partition ID of the volume.

Once you get the device ID, you can unmount the device like this; change `disk[device id]s[partition id]` to be the actual device ID number you just noted in the last step:

    diskutil unmount /dev/disk[device id]s[partition id]

Once the device is unmounted, run the following `fsck_hfs` command on the volume using the same device ID number. The `-r` flag tells `fsck_hfs` to rebuild the catalog btree and the `-f` flag forces `fsck_hfs` to check and repair journaled HFS+ file systems:

    sudo fsck_hfs -rf /dev/disk[device id]s[partition id]

The output should be something like this:

	** /dev/rdisk3s2
	   Executing fsck_hfs (version hfs-226.1.1).
	** Checking Journaled HFS Plus volume.
	   The volume name is Toshiba500
	** Checking extents overflow file.
	** Checking catalog file.
	** Rebuilding catalog B-tree.
	** Rechecking volume.
	** Checking Journaled HFS Plus volume.
	   The volume name is Toshiba500
	** Checking extents overflow file.
	** Checking catalog file.
	** Checking multi-linked files.
	** Checking catalog hierarchy.
	** Checking extended attributes file.
	** Checking volume bitmap.
	** Checking volume information.
	   Invalid volume file count
	   (It should be 101 instead of 2)
	   Invalid volume directory count
	   (It should be 19 instead of 0)
	   Invalid volume free block count
	   (It should be 121903155 instead of 121903469)
	   Volume header needs minor repair
	(2, 0)
	** Repairing volume.
	** Rechecking volume.
	** Checking Journaled HFS Plus volume.
	   The volume name is Toshiba500
	** Checking extents overflow file.
	** Checking catalog file.
	** Checking multi-linked files.
	** Checking catalog hierarchy.
	** Checking extended attributes file.
	** Checking volume bitmap.
	** Checking volume information.
	** The volume Toshiba500 was repaired successfully.

After `fsck_hfs` has done it’s thing, do a quick check—using the `-q` flag—to see if everything is okay:

    sudo fsck_hfs -q /dev/disk[device id]s[partition id]

The output should look something like this:

	** /dev/rdisk3s2 (NO WRITE)
	   Executing fsck_hfs (version hfs-226.1.1).
	QUICKCHECK ONLY; FILESYSTEM CLEAN

If everything went well, the volume should be repaired, working and ready for use again. You can now mount the partition with this command:

    diskutil mount /dev/disk[device id]s[partition id]

### Disabling/enabling journaling from the command line.

You can manually disable journaling from the command line using this command with the `-N` flag on the target volume. Change `disk[device id]s[partition id]` to be the actual device ID number of the volume you want to disable journaling on:

    sudo /System/Library/Filesystems/hfs.fs/hfs.util -N /dev/disk[device id]s[partition id]

Other options/flags for `hfs.util` are as follows:

	usage: /System/Library/Filesystems/hfs.fs/hfs.util action_arg device_arg [mount_point_arg] [Flags]
	action_arg:
	       -p (Probe for mounting)
	       -m (Mount)
	       -u (Unmount)
	       -M (Force Mount)
	       -a (Adopt permissions)
	       -J (Make a file system journaled)
	       -U (Turn off journaling on a file system)
	       -N (Turn off journaling on a raw device)
	       -e (Disable use of an external journal on a raw device)
	       -E (Enable the use of an external journal on a raw device)
	       -I (Get size & location of journaling on a file system)
	device_arg:
	       device we are acting upon (for example, 'disk0s2')
	       if '-J' or '-U' is specified, this should be the
	       name of the file system we're to act on (for example, '/Volumes/foo' or '/')
	mount_point_arg:
	       required for Mount and Force Mount
	Flags:
	       required for Mount, Force Mount and Probe
	       indicates removable or fixed (for example 'fixed')
	       indicates readonly or writable (for example 'readonly')
	       indicates suid or nosuid (for example 'suid')
	       indicates dev or nodev (for example 'dev')
	Examples:
	       /System/Library/Filesystems/hfs.fs/hfs.util -p disk0s2 fixed writable
	       /System/Library/Filesystems/hfs.fs/hfs.util -m disk0s2 /my/hfs removable readonly nosuid nodev

### Erasing a volume from the command line using `diskutil`.

Erase a volume from the command line using `diskutil` like this. Note that `HFS+` should be changed to match whatever format type should be used, `[VolumeName]` should be changed to match the new volume name and `/dev/disk[device id]s[partition id]` to be the actual device ID number of the volume you want to act on:

    diskutil eraseVolume HFS+ [VolumeName] /dev/disk[device id]s[partition id]

***

*Mac OS X - Disk Utility Repairs and Tricks from the Terminal (c) by Jack Szwergold; written on September 13, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*