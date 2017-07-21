## Ubuntu - Trickle

By Jack Szwergold

### Installing Trickle.

Install `trickle`:

    sudo aptitude install trickle

### Using Trickle on a per-use basis.

To use a trickle on a per-command basis, you can use a single command, in this method the syntax is:

    trickle -d [Download Bandwidth Kbps] -u [Upload Bandwidth Kbps] [command]

For example, let’s limit the download bandwidth to 150Kbps for a `curl` command to download an ISO image from the Ubuntu website:

    trickle -d 100 curl -L -O http://releases.ubuntu.com/15.04/ubuntu-15.04-server-amd64.iso

### Using the Trickle daemon from the command line.

To start trickle as daemon mode and limit all the bandwidth, use the command `trickled` with the following syntax:

    sudo trickled -d [Download Bandwidth Kbps] -u [Upload Bandwidth Kbps] [command]

For example, run this command to run the Trickle daemon with 300Kbps download and 120Kbps upload:

    sudo trickled -d 300 -u 120

### Pre-configuring the Trickle daemon.

Instead of dealing with launching and configuring the Trickle daemon via the command line, you can preconfigure the daemon like this:

    sudo nano /etc/trickled.conf

Here are the contents of the sample Trickle daemon configurations:

	# this file is an example for a system-wide or personal settings file
	# for trickle (see manual pages for trickle, trickled and trickled.conf
	# below are two examples that should be self-explaining
	
	# [ssh]
	# Priority = 1
	# Time-Smoothing = 0.1
	# Length-Smoothing = 2
	# [ftp]
	# Priority = 8
	# Time-Smoothing = 5
	# Length-Smoothing = 20

And here are some sample Trickle daemon configurations for `curl` and `wget`:

	[curl]
	  Priority = 8
	  Time-Smoothing = 5
	  Length-Smoothing = 20
	
	[wget]
	  Priority = 8
	  Time-Smoothing = 5
	  Length-Smoothing = 20

According to the official documentation, the value of `Time-Smoothing` is defined as follows in the documentation:

> Set smoothing time to seconds s. The smoothing time determines with what intervals `trickled` will try to let the application transcieve data. Smaller values will result in a more continuous (smooth) session, while larger values may produce bursts in the sending and receiving data. Smaller values (0.1 - 1 s) are ideal for interactive applications while slightly larger values (1 - 10 s) are better for applications that need bulk transfer. This parameter is customizable on a per-application basis via trickled.conf(5). The default value is 5 s.

And similarly, the value of `Length-Smoothing` is defined as follows in the documentation:

> Set smoothing length to length KB. The smoothing length is a fallback of the smoothing time. If `trickled` cannot meet the requested smoothing time, it will instead fall back on sending length KB of data. The default value is 10 KB.

***

*Ubuntu - Trickle (c) by Jack Szwergold; written on October 9, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
