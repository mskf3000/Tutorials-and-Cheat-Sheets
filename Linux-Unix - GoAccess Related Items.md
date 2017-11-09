## Linux-Unix - GoAccess Related Items

By Jack Szwergold

### Installing GoAccess and NPM via a PPA on Ubuntu

First, import the public key used by the package management system:

	curl -sL https://deb.goaccess.io/gnugpg.key | sudo apt-key add -

Next, create a sources list file for GoAccess:

	echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list

Now, run `aptitude update` like this:

    sudo aptitude update

Then install GoAccess via `aptitude` like this:

    sudo aptitude install goaccess-tcb libtokyocabinet9

Check the version number like this:

    goaccess --version

And the output should be something like this:

	GoAccess - 1.2.
	For more details visit: http://goaccess.io
	Copyright (C) 2009-2016 by Gerardo Orellana

#### Basic usage examples.

Run this command to have GoAccess process the `access.log` in the `/var/log/apache2/` which has a basic `COMBINED` log format.

	goaccess /var/log/apache2/access.log --log-format=COMBINED

Run this command to have GoAccess process the `access.log` in the `/var/log/nginx/` which has a basic `COMBINED` log format.

	sudo goaccess /var/log/nginx/access.log --log-format=COMBINED
	
Run this command to process the `access.log.1` and `access.log` in the `/var/log/nginx/` directory:

	sudo goaccess /var/log/nginx/access.log.1 /var/log/nginx/access.log --log-format=COMBINED

Same command but using `nice` to make sure the process doesn’t hog up too many system resources.

	nice -n 19 sudo goaccess /var/log/nginx/access.log.1 /var/log/nginx/access.log --log-format=COMBINED

Run this command to pipe a Gzipped files via standard input to GoAccess and process other, uncompressed files:

	sudo zcat /var/log/nginx/access.log.2.gz | sudo goaccess --log-format=COMBINED /var/log/nginx/access.log.1 /var/log/nginx/access.log -

Run this command to pipe all Gzipped files—note the wildcard—via standard input to GoAccess and process other, uncompressed files:

	sudo zcat /var/log/apache2/www.preworn.com.access.log.*.gz | sudo goaccess --log-format=COMBINED /var/log/apache2/access.log.1 /var/log/apache2/access.log -

A simple variant to just process Gzipped logs:

	zcat /var/log/apache2/www.preworn.com.access.log.*.gz | goaccess --log-format=COMBINED

#### Storage examples.

##### For Apache:

Process and save DB files:

	goaccess /var/log/apache2/access.log --log-format=COMBINED --keep-db-files

Load DB files from disk:

	goaccess /var/log/apache2/access.log --log-format=COMBINED --keep-db-files --load-from-disk

##### For Nginx:

Process and save DB files:

	goaccess /var/log/nginx/access.log --log-format=COMBINED --keep-db-files

Load DB files from disk:

	goaccess /var/log/nginx/access.log --log-format=COMBINED --keep-db-files --load-from-disk

***

*Linux-Unix - GoAccess Related Items (c) by Jack Szwergold; written on November 8, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*