## Mac OS X - Adjust the Hibernation Mode Sleepimage

By Jack Szwergold

***

When portables like MacBooks “hibernate” the contents of RAM are saved to disk in a `sleepimage` file for retrieval when the system comes back up again. By saving the contents of RAM to the `sleepimage` file, battery power is saved and if the system experiences power failure, the contents of the RAM saved to disk can quickly be restored.

While this functionality is useful for portables like MacBooks, it’s not really that useful for desktop machines. If you have 16GB of RAM, that `sleepimage` file will take up approximately 16GB of disk space. And that’s a waste of 16GB of space if you don’t need hibernation mode.

This tutorial explains how to adjust hibernation mode and disable `sleepimage`.

### Details on the hibernation mode settings.

First, the details in `man pmset` explain `hibernatemode` settings as follows:

	We do not recommend modifying hibernation settings. Any changes you make are not supported. If you choose to do so anyway, we recommend using one of these three settings. For your sake and mine, please don’t use anything other 0, 3, or 25.

I feel that warning is a bit dramatic, but here are the details on the recommended modes of 0, 3 and 25:

	hibernatemode = 0 (binary 0000) by default on supported desktops. The system will not back memory up to persistent storage. The system must wake from the contents of memory; the system will lose context on power loss. This is, historically, plain old sleep.
	
	hibernatemode = 3 (binary 0011) by default on supported portables. The system will store a copy of memory to persistent storage (the disk), and will power memory during sleep. The system will wake from memory, unless a power loss forces it to restore from disk image.
	
	hibernatemode = 25 (binary 0001 1001) is only settable via pmset. The system will store a copy of memory to persistent storage (the disk), and will remove power to memory. The system will restore from disk image. If you want “hibernation” - slower sleeps, slower wakes, and better battery life, you should use this setting.

### Disable the hibernation mode `sleepimage`.

Run this command to disable `hibernatemode`:

    sudo pmset -a hibernatemode 0

Next, delete the actual `sleepimage` file:

    sudo rm /private/var/vm/sleepimage

Now, create an empty `sleepimage` file to replace the one that was just deleted:

    sudo touch /private/var/vm/sleepimage

Finally, change the `sleepimage` flag to immutable (aka: unchaging) to prevent the file from being removed or recreated by the system:

    sudo chflags schg /private/var/vm/sleepimage

Once that’s done, the `sleepimage` cannot be recreated by the system unless those steps are reversed and undone.

With that done, if for some reason you need to delete that immutable `sleepimage` file, run this command to delete it:

    sudo chflags noschg /private/var/vm/sleepimage

***

*Mac OS X - Adjust the Hibernation Mode Sleepimage (c) by Jack Szwergold; written September 12, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*