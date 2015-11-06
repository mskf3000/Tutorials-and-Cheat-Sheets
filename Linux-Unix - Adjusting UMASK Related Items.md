# Linux-Unix - Adjusting UMASK Related Items

By Jack Szwergold, September 16, 2015

#### Adjusting UMASK items on a CentOS/RedHat system.

Open up `/etc/bashrc`:

    sudo nano /etc/bashrc

Search through the code and find the chunk of code that looks something like this:

	# By default, we want this to get set.
	# Even for non-interactive, non-login shells.
	if [ $UID -gt 99 ] && [ "`id -gn`" = "`id -un`" ]; then
	        umask 002
	else
	    	umask 022
	fi

Change the `umask` value in the `else` logic to:

    umask 002

#### Adjusting UMASK items on an Ubuntu/Debian system.

Open up `/etc/profile`:

    sudo nano /etc/profile

The last line of the file should have a `umask` entry that reads as follows:

    umask 022

Change the `umask` value to:

    umask 002

If that line is not in the file, just add it to the file and save it.

#### Adjustments to the `pam.d` UMASK settings on an Ubuntu/Debian system.

Open up `/etc/pam.d/common-session`:

    sudo nano /etc/pam.d/common-session

Find the line that reads something like this:

    session optional                        pam_umask.so

Change it to this by adding `umask=0002` to the end of the line:

    session optional                        pam_umask.so	umask=0002

***

*Linux-Unix - Adjusting UMASK Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*