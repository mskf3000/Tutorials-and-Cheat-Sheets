## RNG-Tools

By Jack Szwergold

### Install RNG-Tools.

Check system entropy levels with this `watch` command:

	watch -n 1 cat /proc/sys/kernel/random/entropy_avail

Then install RNG-Tools via `aptitude` like this:

	sudo aptitude install rng-tools
	
Start, stop and control RNG-Tools on an Ubuntu/Debian system:

	sudo service rng-tools start
	sudo service rng-tools stop
	sudo service rng-tools restart
	sudo service rng-tools force-reload

### Add RNG-Tools as a startup service.

Add `rng-tools` as an Ubuntu service from starting on boot:

    sudo update-rc.d rng-tools defaults

Remove `rng-tools` as an Ubuntu service from starting on boot:

	sudo update-rc.d rng-tools remove

### Configure RNG-Tools.

Once installed open up the RNG-Tools config file:

	sudo nano /etc/default/rng-tools

And add values for `HRNGDEVICE` and `RNGDOPTIONS` like this:

	HRNGDEVICE=/dev/urandom
	RNGDOPTIONS="--fill-watermark=90% --feed-interval=1"

Add those values to the bottom of each respective commented out area for easier contextual reference:

	# Configuration for the rng-tools initscript
	# $Id: rng-tools.default,v 1.1.2.5 2008-06-10 19:51:37 hmh Exp $
	
	# This is a POSIX shell fragment
	
	# Set to the input source for random data, leave undefined
	# for the initscript to attempt auto-detection.  Set to /dev/null
	# for the viapadlock and tpm drivers.
	#HRNGDEVICE=/dev/hwrng
	#HRNGDEVICE=/dev/null
	HRNGDEVICE=/dev/urandom
	
	# Additional options to send to rngd. See the rngd(8) manpage for
	# more information.  Do not specify -r/--rng-device here, use
	# HRNGDEVICE for that instead.
	#RNGDOPTIONS="--hrng=intelfwh --fill-watermark=90% --feed-interval=1"
	#RNGDOPTIONS="--hrng=viakernel --fill-watermark=90% --feed-interval=1"
	#RNGDOPTIONS="--hrng=viapadlock --fill-watermark=90% --feed-interval=1"
	#RNGDOPTIONS="--hrng=tpm --fill-watermark=90% --feed-interval=1"
	RNGDOPTIONS="--fill-watermark=90% --feed-interval=1"

Note that on Ubuntu 14.04 that `RNGDOPTIONS` with `--feed-interval` so just set that to something like this:

	RNGDOPTIONS="--fill-watermark=90% "

***

*RNG-Tools (c) by Jack Szwergold; written September 24, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*