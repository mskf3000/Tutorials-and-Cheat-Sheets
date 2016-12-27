## macOS - Figlet

By Jack Szwergold

This tutorial explains how you can install Figlet directly from source code on macOS. Tested on macOS 10.9.5.

### Installing Figlet via official source code archive.

First grab a compressed archive from an official Figlet source site:

	curl -O -L ftp://ftp.figlet.org/pub/figlet/program/unix/figlet-2.2.5.tar.gz

Next, decompress the archive like this:

	tar -xf figlet-2.*.tar.gz
	
Now go into the decompressed directory:

	cd figlet-*
	
Run `make`:

	make
	
And finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command like this to do a simple check to see it’s working:

	figlet Hello!

And the output should be something like this:

	 _   _      _ _       _
	| | | | ___| | | ___ | |
	| |_| |/ _ \ | |/ _ \| |
	|  _  |  __/ | | (_) |_|
	|_| |_|\___|_|_|\___/(_)

### Basic usage and cool tricks.

To see all of the Figlet fonts installed on the system run this command:

    showfigfonts

And to select another font to use, just use the `-f` flag like this:

    figlet -f slant Hello!

This example uses `slant` font and the output would be something like this:

	    __  __     ____      __
	   / / / /__  / / /___  / /
	  / /_/ / _ \/ / / __ \/ /
	 / __  /  __/ / / /_/ /_/
	/_/ /_/\___/_/_/\____(_)

***

*macOS - Figlet (c) by Jack Szwergold; written on November 1, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*