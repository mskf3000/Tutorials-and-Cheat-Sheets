## Sysstat

By Jack Szwergold

### Install Sysstat.

Install the Sysstat utilities to monitor system statistics via `aptitude`:

    sudo aptitude install sysstat

### Basic `munin` usage items.

Control `sysstat`:

	sudo service sysstat reload
	sudo service sysstat restart
	sudo service sysstat start
	sudo service sysstat stop

### Clearing up “sadc not enabled…” errors.

If you get this message:

> sadc not enabled in `/etc/default/sysstat`, not starting.

Then edit this file:

    sudo nano /etc/default/sysstat

Then look for this and change the value of `ENABLED` from `false` to `true`:

	# Should sadc collect system activity informations? Valid values
	# are "true" and "false". Please do not put other values, they
	# will be overwritten by debconf!
	ENABLED="true"

### Clearing up “Invalid system activity file…” errors.

If you get a message like the following:

> Invalid system activity file:` /var/log/sysstat/sa14`

Then just purge and re-install `sysstat` to get it back up and running:

    sudo aptitude purge sysstat

***

*Sysstat (c) by Jack Szwergold; written on September 16, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*