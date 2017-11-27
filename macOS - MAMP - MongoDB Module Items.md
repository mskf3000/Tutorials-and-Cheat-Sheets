## macOS - MAMP - MongoDB Module Items

By Jack Szwergold

### PHP MongoDB under MAMP 3.5.2 running PHP 5.6.10

Before anything else, make sure your local MAMP install’s binary paths are part of you default PATH. You would do this by adding the following paths to your main PATH in your `~/.bash_profile`:

* **MAMP 3.5.2 Main `bin/` Path**: `/Applications/MAMP/Library/bin`
* **MAMP 3.5.2 PHP `bin/` Path**: `/Applications/MAMP/bin/php/php5.6.10/bin`

#### Homebrew Prerequisites

Before anything, make sure you have Homebrew installed and run this command to make sure you have `openssl`, `pkg-config` and `autoconf` installed as well:

	brew install openssl pkg-config autoconf

#### Compile PHP

Now we will download the exact same version of PHP you will be running under MAMP so we can properly compile the MongoDB module. We will not be completely rebuilding PHP from source, but we need the full PHP source code to build the module against.

Before anything else, make sure to head to your desktop before anything else happens:

	cd ~/Desktop

Now use Curl to grab the PHP source code for the version of PHP you will be using under MAMP like this:

	curl -O -L http://am1.php.net/distributions/php-5.6.10.tar.gz

Then decompress the PHP source code archive:

	tar -xf php-5.6.10.tar.gz

Now go into the decompressed source code directory:

	cd php-5.6.10

And run this `configure` command; we don’t need iconv for what we are doing so we build it like this:

	./configure --without-iconv

If for some reason you need iconv, you can run this `configure` command:

	./configure --with-iconv=/Applications/MAMP/Library/

With that done, run `make`:

	make

And finally with that done, move—and rename—the whole `php-5.6.10` directory to `php/` into the `include/` directory for PHP 5.6.10 under MAMP:

	/Applications/MAMP/bin/php/php5.6.10/include/

The full path will be:

	/Applications/MAMP/bin/php/php5.6.10/include/php/

#### Compiling the MongoDB Module

With PHP now compiled, let’s get the source code for the MongoDB module via PECL:

	pecl download mongo-1.6.16

And if that somehow doesn’t work, just download the source code directly via Curl this way:

	curl -O https://pecl.php.net/get/mongo-1.6.16.tgz

With that downloaded, decompress the archive like this:

	tar -xvzf mongo-1.6.16.tgz

And go into the source code directory:

	cd mongo-1.6.16

Set these environment variables for OpenSSL related stuff:

	export LDFLAGS=-L/usr/local/opt/openssl/lib
	export CPPFLAGS=-I/usr/local/opt/openssl/include

We’re going to run `phpize` to prepare the MongoDB PHP extension of compiling:

	phpize

Note if the `phpize` command fails with a bunch of `No such file or directory` errors like this:

	grep: /Applications/MAMP/bin/php/php5.6.10/include/php/main/php.h: No such file or directory
	grep: /Applications/MAMP/bin/php/php5.6.10/include/php/Zend/zend_modules.h: No such file or directory
	grep: /Applications/MAMP/bin/php/php5.6.10/include/php/Zend/zend_extensions.h: No such file or directory
	Configuring for:
	PHP Api Version:        
	Zend Module Api No:     
	Zend Extension Api No:  

Then you need to create an `include/php` directory in this path:

	/Applications/MAMP/bin/php/php5.6.10/include/php

And the contents of that `include/php` directory will be the `php-5.6.10` source code directory itself. You might need to run `make clean` and run the configure commands in that directory again to get everything set. Then rerun `phpize` again and the output should be clean and look like this:

	Configuring for:
	PHP Api Version:         20131106
	Zend Module Api No:      20131226
	Zend Extension Api No:   220131226

With that done, run this `configure` command:

	./configure --with-openssl-dir=/usr/local/opt/openssl

With that done, run `make` to compile the MongoDB PHP extension:

	make

Run `make test` to make sure the build was good:

	make test

And finally install the module like this:

	make install

#### Installing the MongoDB Module

If that runs successfully you have now successfully created a `mongodb.so` PHP module that has been compiled against `php-5.6.10` and is ready to use with MAMP.

Now adjust the PHP config file (`php.ini`) like this to get PHP to recognize it:

	echo "extension=mongodb.so" >> /Applications/MAMP/bin/php/php5.6.10/conf/php.ini

Now start MAMP again and check the output of the PHP info page and `MongoDB` should be clearly listed there under installed components.

***

*macOS - MAMP - MongoDB Module Items (c) by Jack Szwergold; written on November 22, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*