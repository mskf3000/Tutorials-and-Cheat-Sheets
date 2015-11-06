# Cheat Sheet - Mac OS X - Wget

By Jack Szwergold, September 11, 2015

This tutorial explains how you can install `wget` directly from source code on Mac OS X. Tested on Mac OS X 10.9.5.

***

First grab a compressed archive from an official `wget` source site:

	curl -O -L http://ftp.gnu.org/gnu/wget/wget-1.16.3.tar.gz

Next, decompress the archive like this:

	tar -xzf wget-1.*.tar.gz

Now go into the decompressed directory:

	cd wget-1.*
	
Run this `configure` command:

	./configure --with-ssl=openssl

If somehow that `configure` command fails, you might need to add a `--with-libssl-prefix`value like this:

	./configure --with-ssl=openssl --with-libssl-prefix=/usr/lib

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `--help` to do a simple check to see it’s working:

	wget --help

***

*Cheat Sheet - Mac OS X - Wget (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*