# Cheat Sheet - Mac OS X - AUTOCONF, AUTOMAKE and LIBTOOL

By Jack Szwergold, September 14, 2015

Mac OS X is a flavor of Unix (Darwin) based on BSD and as such does not contain many commonly available GNU Linux tools in it’s default install.

So if you are planning on compiling any modern GNU projects you’re going to have to install a few core GNU related tools: `autoconf`, `automake` and `libtool`. This tutorial explains how to do that.

#### Installing `autoconf` via official source code archive.

First grab a compressed archive from an official `autoconf` source site:

	curl -O -L http://ftpmirror.gnu.org/autoconf/autoconf-2.69.tar.gz

Next, decompress the archive like this:

	tar -xzf autoconf-2.*.tar.gz

Now go into the decompressed directory:

	cd autoconf-2.*

Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `--version` to do a simple check to see it’s working:

	autoconf --version

Response should be something like this:

> autoconf 2.69

Uninstall if you need to:

	sudo make uninstall

#### Installing `automake` via official source code archive.

First grab a compressed archive from an official `automake` source site:

	curl -O -L http://ftpmirror.gnu.org/automake/automake-1.15.tar.gz

Next, decompress the archive like this:

	tar -xzf automake-1.15.tar.gz

Now go into the decompressed directory:

	cd automake-*

Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `--version` to do a simple check to see it’s working:

	automake --version

Response should be something like this:

> automake 1.15

Uninstall if you need to:

	sudo make uninstall

#### Installing `libtool` via official source code archive.

First grab a compressed archive from an official `libtool` source site:

	curl -O -L http://ftpmirror.gnu.org/libtool/libtool-2.4.6.tar.gz

Next, decompress the archive like this:

	tar -xzf libtool-2.4.6.tar.gz

Now go into the decompressed directory:

	cd libtool-*

Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `--version` to do a simple check to see it’s working:

	libtool --version

Response should be something like this:

> libtool (GNU libtool) 2.4.6

Uninstall if you need to:

	sudo make uninstall

***

*Cheat Sheet - Mac OS X - AUTOCONF, AUTOMAKE and LIBTOOL (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
