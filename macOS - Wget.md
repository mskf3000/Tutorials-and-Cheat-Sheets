## macOS - Wget

By Jack Szwergold

This tutorial explains how you can install `wget` directly from source code on macOS. Tested on macOS 10.12.1.

Install it with OpenSSL like this:

	curl -O -L https://www.openssl.org/source/openssl-1.0.2j.tar.gz
	tar -xf openssl-1.0.2j.tar.gz
	cd openssl-1.0.2j

	./configure darwin64-x86_64-cc
	make depend
	make
	sudo make install

To uninstall it, just delete this directory:

	sudo rm -rf /usr/local/ssl

Now install WGET like this:

	curl -O -L http://ftp.gnu.org/gnu/wget/wget-1.18.tar.gz
	tar -xf wget-1.18.tar.gz
	cd wget-1.18
	./configure --with-ssl=openssl --with-libssl-prefix=/usr/local/ssl
	make
	sudo make install
	sudo make uninstall

***

*macOS - Wget (c) by Jack Szwergold; written on September 11, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*