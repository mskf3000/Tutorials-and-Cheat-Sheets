## macOS - HTop

By Jack Szwergold

This tutorial explains how you can install HTop directly from source code on macOS. Tested on macOS 10.12 (Sierra).

### Compiling from source from the official GitHub repository.

First clone the source code from max-horvath’s `htop-osx` GitHub repository:

	git clone git@github.com:hishamhm/htop.git
	
Now go into the repository directory:

	cd htop

You might have to run the `libtoolize` and `automake -f` commands like this:

	libtoolize && automake -f

Now run `autogen.sh`:

	./autogen.sh
	
Then run this `configure` command:

	./configure
	
Once the `configure` process completes, run `make`:

	make
	
Finally install it by running `sudo make install`:

	sudo make install

Uninstall HTop by running this command from the source code directory:

	sudo make uninstall

Or just run these commands:

	rm -f /usr/local/share/applications/htop.desktop;
	rm -f /usr/local/bin/htop;
	rm -f /usr/local/share/man/man1/htop.1
	rm -f /usr/local/share/pixmaps/htop.png

***

*macOS - HTop (c) by Jack Szwergold; written on September 15, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*