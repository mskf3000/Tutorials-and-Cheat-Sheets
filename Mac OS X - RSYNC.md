## Mac OS X - RSYNC

By Jack Szwergold, September 13, 2015

This tutorial explains how you can install `rsync` directly from source code on Mac OS X. Tested on Mac OS X 10.9.5. Note that Mac OS X 10.9.5 already has a version of `rsync` installed, but it’s a bit out of date; Mac OS X `rsync` version is “2.6.9  protocol version 29.” This tutorial is intended to allow one to install a newer version of `rsync` but only be able to call it explicitly if/when it’s needed.

First grab a compressed archive from an official `rsync` source site:

	curl -O -L http://rsync.samba.org/ftp/rsync/rsync-3.1.1.tar.gz

Next, decompress the archive like this:

	tar -xf rsync-3.*.tar.gz

Now go into the decompressed directory:

	cd rsync-3.*
	
Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install	

And once it’s installed, run the command with `--version` to do a simple check to see it’s working:

	/usr/local/bin/rsync --version

Note how I’m invoking the `rsync` command with the full binary path of `/usr/local/bin/rsync` instead of just `rsync`. This is because Mac OS X already has a built-in—but out of date—version of `rsync`.

On Mac OS X 10.9.5 the version info is “2.6.9  protocol version 29.” While it’s nice to have a newer version of `rsync` on the system I prefer not to use it unless I explicitly call it; let the older Mac OS X version have priority when `rsync` is being invoked just as `rsync`.

***

<sup>*Mac OS X - RSYNC (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>