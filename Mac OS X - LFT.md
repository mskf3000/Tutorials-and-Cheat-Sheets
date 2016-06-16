## Mac OS X - LFT

By Jack Szwergold

This tutorial explains how you can install `lft` directly from source code on Mac OS X. Tested on Mac OS X 10.9.5.

First grab a compressed archive from an official `lft` source site:

	curl -o ./lft-3.73.tar.gz -O http://pwhois.org/dl/index.who?file=lft-3.73.tar.gz

Next, decompress the archive like this:

	ar -xf lft-3.*.tar.gz

Now go into the decompressed directory:

	cd lft-3.*
	
Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `-v` to do a simple check to see it’s working:

	lft -v

***

*Mac OS X - LFT (c) by Jack Szwergold; written on September 14, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*