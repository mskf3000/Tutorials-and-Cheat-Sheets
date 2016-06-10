## SSH - Chroot and SFTP Notes

By Jack Szwergold, September 17, 2015

### Setting up SFTP chroot access for a specific user.

Open up the main `sshd_config` on the server:

    sudo nano /etc/ssh/sshd_config

And add—or edit—the following to the bottom of `sshd_config`:

	Match User [username]
	  ChrootDirectory /var/www
	  ForceCommand internal-sftp
	  AllowTcpForwarding no
	  X11Forwarding no

The chroot directory needs to be owned by root and permissions need to be:

    drwxr-xr-x

Edit `/etc/passwd` so that the subdirectory of the chroot directory is somehow related to the new chroot’ed home. To do this, open up `/etc/passwd` for editing like this:

    sudo nano /etc/passwd

And find the entry for the `[username]` that you wish to adjust for chroot’ing purposes. The stuff in the area that reads `/home/[username]` is what we will be changing:

    [username]:x:1005:33::/home/[username]:/bin/bash

Now adjust that `/home/[username]` to match the new chroot’ed relative path; in general it should just be `/` but it can be any child directory in that chroot’ed path:

    [username]:x:1005:33::/:/bin/bash

Then restart `ssh`:

     sudo service ssh restart

### Preventing SFTP access.

To disable SFTP access to a server, open up the main `sshd_config` on the server:

    sudo nano /etc/ssh/sshd_config

Do this to disable SFTP access on a per-user basis:

	Match User [username]
	  Subsystem sftp /bin/false

Or do this to disable SFTP access on a per-group basis:

	Match Group [groupname]
	  Subsystem sftp /bin/false

***

*SSH - Chroot and SFTP Notes (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*