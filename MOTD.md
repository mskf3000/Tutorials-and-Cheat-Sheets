# MOTD

By Jack Szwergold, September 24, 2015

### Adding a custom MOTD message.

For example, let’s say we wanted to use `figlet` to make a nice server banner like this:

    figlet Server 1

The output of that would look something like this:

	 ____                             _ 
	/ ___|  ___ _ ____   _____ _ __  / |
	\___ \ / _ \ '__\ \ / / _ \ '__| | |
	 ___) |  __/ |   \ V /  __/ |    | |
	|____/ \___|_|    \_/ \___|_|    |_|

Now open up the `/etc/motd.tail` tail file and just add that text to the file:

    sudo nano /etc/motd.tail

Save it and that message will show up during your next login to the server.

### Clean up the advertising and related notices in MOTD.

Check the contents of the MOTD directory:

	sudo ls -la /etc/update-motd.d/
	
	total 44
	drwxr-xr-x   2 root root  4096 Jan 12 14:44 .
	drwxr-xr-x 117 root root 12288 Jan 15 11:17 ..
	-rwxr-xr-x   1 root root  1220 Jul 14  2011 00-header
	-rwxr-xr-x   1 root root  1358 Jul 14  2011 10-help-text
	lrwxrwxrwx   1 root root    46 Jan 12 14:44 50-landscape-sysinfo -> /usr/share/landscape/landscape-sysinfo.wrapper
	-rwxr-xr-x   1 root root   149 May 15  2012 90-updates-available
	-rwxr-xr-x   1 root root   129 Apr 19  2011 91-release-upgrade
	-rwxr-xr-x   1 root root   142 May 15  2012 98-fsck-at-reboot
	-rwxr-xr-x   1 root root   144 May 15  2012 98-reboot-required
	-rwxr-xr-x   1 root root  1158 Oct 21  2010 99-footer

Get rid of the symbolic link to the `landscape sysinfo`:

	sudo rm /etc/update-motd.d/50-landscape-sysinfo
	sudo rm /etc/update-motd.d/51-cloudguest

Comment out the content of these MOTD scripts:

	sudo nano /etc/update-motd.d/90-updates-available
	sudo nano /etc/update-motd.d/91-release-upgrade
	sudo nano /etc/update-motd.d/95-hwe-eol

***

*MOTD (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*