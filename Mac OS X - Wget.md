## Mac OS X - Wget

By Jack Szwergold

This tutorial explains how you can install `wget` directly from source code on Mac OS X. Tested on Mac OS X 10.12.1.

Install it with OpenSSL like this:

	curl -O -L https://www.openssl.org/source/openssl-1.0.2j.tar.gz
	tar -xzf openssl-1.0.2j.tar.gz
	cd openssl-1.0.2j

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
	sudo make uninstall

***

*Mac OS X - Wget (c) by Jack Szwergold; written on September 11, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*