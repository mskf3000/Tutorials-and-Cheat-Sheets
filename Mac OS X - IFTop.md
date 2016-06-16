## Mac OS X - IFTop

By Jack Szwergold

This tutorial explains how you can install IFTop directly from source code on Mac OS X. Tested on Mac OS X 10.9.5.

### Installing IFTop via official source code archive.

First grab a compressed archive from an official IFTop source site:

	curl -O -L http://www.ex-parrot.com/~pdw/iftop/download/iftop-0.17.tar.gz

Next, decompress the archive like this:

	tar -xf iftop-0.*.tar.gz
	
Now go into the decompressed directory:

	cd iftop-0.*
	
Run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make
	
Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `-h` to do a simple check to see it’s working:

	iftop -h

***

*Mac OS X - IFTop (c) by Jack Szwergold; written on September 14, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*