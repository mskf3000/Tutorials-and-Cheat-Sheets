## Mac OS X - ID3Lib

By Jack Szwergold

This tutorial explains how you can install ID3Lib directly from source code on Mac OS X. Tested on Mac OS X 10.11.5.

### Installing ID3Lib via official source code archive.

First clone the source code from attilagyorffy’s `id3lib-osx` GitHub repository:

	git clone git@github.com:attilagyorffy/id3lib-osx.git
	
Now go into the repository directory:

	cd id3lib-osx
	
Run this `configure` command:

	./configure
	
Once the `configure` process completes, run `make`:

	make
	
Finally install it by running `sudo make install`:

	sudo make install

### Binaires installed.

* `id3info`
* `id3tag`
* `id3convert`
* `id3cp`

***

*Mac OS X - ID3Lib (c) by Jack Szwergold; written on August 22, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*