# Cheat Sheet - PHP - Setting up JSON suport for PHP 5.1.6 in Ubuntu 12.04

By Jack Szwergold, October 13, 2015

First, install JSON via Pecl like this:

	sudo pecl install json

You might get a warning that reads like this:

     WARNING: "pear/json" is deprecated in favor of "channel://http://www.php.net/json/json"

Just ignore it. The warning is correct in that newer version of PHP have JSON support built-in, but we are dealing with adding JSON support to an ancient version of PHP so this is what we got to do.

Now the tricky part is finding the exact path the `json-1.2.1.tgz`. In some cases it will be installed in a directory like this:

	/build/buildd/php5-5.3.5/pear-build-download/

In others it will be installed in a directory like this:

    /build/php5-yIoy8f/php5-5.3.10/pear-build-download/

	sudo tar -xvf ./json-1.2.1.tgz
	
	cd ./json-1.2.1
	
	sudo phpize json
	sudo /opt/php516-gd/bin/phpize json
	
	sudo ./configure
	
	sudo make
	
	sudo make test

***

*Cheat Sheet - PHP - Setting up JSON suport for PHP 5.1.6 in Ubuntu 12.04 (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
