## Mac OS X - Wget

By Jack Szwergold

This tutorial explains how you can install `wget` directly from source code on Mac OS X. Tested on Mac OS X 10.9.5.

First grab a compressed archive from an official `wget` source site:

	curl -O -L http://ftp.gnu.org/gnu/wget/wget-1.16.3.tar.gz
	curl -O -L http://ftp.gnu.org/gnu/wget/wget-1.17.1.tar.xz

Next, decompress the archive like this:

	tar -xzf wget-1.*.tar.gz

Now go into the decompressed directory:

	cd wget-1.*
	
Run this `configure` command:

	./configure --with-ssl=openssl

***

Or this `configure` command if the process fails in Mac OS X 10.11 (El Capitan) with the error, “configure: error: --with-ssl=openssl was given, but SSL is not available.”: 

	./configure --with-ssl=openssl --with-libssl-prefix=/usr/local/ssl

Or try it like this:

	export OPENSSL_LIBS="-L /usr/lib -lssl -lcrypto -lz"
	export OPENSSL_CFLAGS="-I /usr/local/include"

Or if somehow that `configure` command fails, you might need to add a `--with-libssl-prefix`value like this:

	./configure --with-ssl=openssl --with-libssl-prefix=/usr/lib

***

Install it with OpenSSL like this. First instal OpenSSL like this:

	curl -O -L https://www.openssl.org/source/openssl-1.0.2h.tar.gz
	tar -xzf openssl-1.0.2h.tar.gz
	cd openssl-1.0.2h

	./configure darwin64-x86_64-cc
	make depend
	make
	sudo make install
	sudo make uninstall

Now install WGET like this:

	curl -O -L http://ftp.gnu.org/gnu/wget/wget-1.18.tar.gz
	tar -xzf wget-1.18.tar.gz
	cd wget-1.18
	./configure --with-ssl=openssl --with-libssl-prefix=/usr/local/ssl
	make
	sudo make install

***

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

And once it’s installed, run the command with `--help` to do a simple check to see it’s working:

	wget --help

***

*Mac OS X - Wget (c) by Jack Szwergold; written on September 11, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*