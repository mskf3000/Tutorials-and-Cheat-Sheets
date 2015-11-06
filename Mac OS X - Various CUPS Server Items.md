# Mac OS X - Various CUPS Server Items

By Jack Szwergold, October 6, 2015

#### Log file locations.

The main CUPS log file directory is here:

	/var/log/cups

You can follow the access log like this:

	tail -f -n 1000 /var/log/cups/access_log

You can follow the error log like this:

	tail -f -n 1000 /var/log/cups/error_log

#### Checking for jobs in progress.

You can check what CUPS printing jobs are in the spool already by checking this spool directory:

	sudo ls -la /var/spool/cups/tmp

#### Remotely manage CUPS.

Just go to this URL on the CUPS server; the key is port `631`:

    http://localhost:631

### Clear out and regenerate the CUPS spool directory.

First, go into the `/var/spool/` directory:

	cd /var/spool/

Then move/rename `cups` to `cups.old` like this:

	sudo mv cups cups.old

Now fully restart the server to get CUPS to create a new `/var/spool/` directory:

After reboot, go back into the `/var/spool/` directory:

	cd /var/spool/

And make sure the new `cups` directory is in place. If it is, all is good

	sudo rm cups.old


### Restart the CUPS service.

Stop CUPS:

	sudo launchctl unload /System/Library/LaunchDaemons/org.cups.cupsd.plist

Start CUPS again:

	sudo launchctl load /System/Library/LaunchDaemons/org.cups.cupsd.plist

### Restore “Printer & Fax” settings to the default

Stop CUPS:

    sudo launchctl stop org.cups.cupsd

Rename `cupsd.conf` to `cupsd.conf.old`:

	sudo mv /etc/cups/cupsd.conf /etc/cups/cupsd.conf.old

Copy `cupsd.conf.default` to `cupsd.conf`:

	sudo cp /etc/cups/cupsd.conf.default /etc/cups/cupsd.conf

Rename `printers.conf` to `printers.conf.old`:

    sudo mv /etc/cups/printers.conf /etc/cups/printers.conf.old

Start CUPS again:

    sudo launchctl start org.cups.cupsd

***

*Mac OS X - Various CUPS Server Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*