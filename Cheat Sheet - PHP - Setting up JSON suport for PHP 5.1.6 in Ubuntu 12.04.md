# Cheat Sheet - PHP - Setting up JSON suport for PHP 5.1.6 in Ubuntu 12.04

By Jack Szwergold, October 13, 2015

	sudo pecl install json
	
	cd /build/buildd/php5-5.3.5/pear-build-download/
	
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
