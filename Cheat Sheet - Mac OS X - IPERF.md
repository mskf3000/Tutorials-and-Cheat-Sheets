# Cheat Sheet - Mac OS X - IPERF

By Jack Szwergold, September 15, 2015

This tutorial explains how you can install `iperf` directly from source code on Mac OS X. Tested on Mac OS X 10.9.5.

***

First grab a compressed archive from an official `iperf` source site:

	curl -O -L https://iperf.fr/download/iperf_2.0.5/iperf-2.0.5-source.tar.gz

Next, decompress the archive like this:

	tar -xf iperf-2.*.tar.gz

Now go into the decompressed directory:

	cd iperf-2.*
	
Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `-v` to do a simple check to see it’s working:

	iperf -v

***

*Cheat Sheet - Mac OS X - IPERF (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*