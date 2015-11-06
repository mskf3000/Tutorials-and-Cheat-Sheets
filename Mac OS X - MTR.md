# Cheat Sheet - Mac OS X - MTR

By Jack Szwergold, September 11, 2015

This tutorial explains how you can install `mtr` directly from source code on Mac OS X. Tested on Mac OS X 10.9.5.

***

#### Installing `mtr` via official source code archive.

First grab a compressed archive from an official `mtr` source site:

	curl -O -L ftp://ftp.bitwizard.nl/mtr/mtr-0.86.tar.gz

Next, decompress the archive like this:

	tar -xf mtr-0.*.tar.gz
	
Now go into the decompressed directory:

	cd mtr-0.*
	
Set the `LIBS` value:

	export LIBS='-lm -ltermcap -lresolv'
	
Run these various `automake` related commands:

	aclocal && autoheader && automake --add-missing --copy --foreign && autoconf

Run this `configure` command:

	./configure --without-gtk
	
Once the `configure` process completes, run `make`:

	make
	
Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with some destination address/hostname—like `google.com`—to do a simple check to see it’s working:

	mtr google.com


#### Installing 0.86 from GitHub source.

First grab a copy of the source code the official `mtr` GitHub repository:

	git clone git://github.com/traviscross/mtr.git
	
Now go into the cloned directory:

	cd mtr
	
Run the `bootstrap.sh` shell script:

	./bootstrap.sh
	
Run this `configure` command:

	./configure --without-gtk

Or this other `configure` command:

	./configure CFLAGS="-arch i386 -arch x86_64" LIBS="-lresolv" \
	       --without-gtk --disable-endian-check --disable-dependency-tracking

Once the `configure` process completes, run `make`:

	make
	
Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with some destination address/hostname—like `google.com`—to do a simple check to see it’s working:

	mtr google.com

#### Fix “mtr: unable to get raw sockets.” error.

If you run `mtr` and get the following error:

    mtr: unable to get raw sockets.

Repair the permissions so `4` (SETUID) gets set:

    chmod 4755 /usr/local/sbin/mtr

If this is happening on a Linux/Unix system run that same command like this:

    chmod 4755 /usr/sbin/mtr

***

*Cheat Sheet - Mac OS X - MTR (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*