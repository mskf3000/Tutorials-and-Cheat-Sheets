## macOS - MAMP Related Items

By Jack Szwergold

This is how you can setup named virtual hosts on MAMP. First open up the `httpd.conf` file like this:

    nano /Applications/MAMP/conf/apache/httpd.conf

Add this to the bottom of the file. Requests to a `localhost` request would go to one virtual host. Requests to any host with a `*.local` extension go to the other virtual host.

	NameVirtualHost *
	
	<VirtualHost *>
	  DocumentRoot "/Applications/MAMP/htdocs"
	  ServerName localhost
	  ServerAlias localhost
	</VirtualHost>
	
	<VirtualHost *>
	  DocumentRoot "/Applications/MAMP/htdocs/something/else"
	  ServerName *.local
	  ServerAlias *.local
	</VirtualHost>

### Dealing with `DYLD_LIBRARY_PATH` issues connected to MAMP and ImageMagick.

Sometimes binaries compiled with dynamic library requirements choke when being called via `exec()` or `shell_exec()` in MAMP. I first noticed this when having issues with ImageMagick `convert` being called via the `exec()` with errors showing up via `stderr` instead of the standard PHP error log; massive pain in the ass:

    tail -f -n 200 /var/log/system.log

This is due to MAMP setting a `DYLD_LIBRARY_PATH` environment variable. Unsure what is lost by disabling this, but I was able to fix ImageMagick interaction with MAMP by neutering it like this.

First open up the MAMP specific, `envvars` file like this:

    nano /Applications/MAMP/Library/bin/envvars

Then find the line near the bottom that reads like this:

    export DYLD_LIBRARY_PATH

And comment it out like this:

    #export DYLD_LIBRARY_PATH

Finally restart the MAMP servers and all should be good as far as `exec()` and programs that require dynamic libraries like ImageMagick.

### Adjusting the PHP timezone.

Since MAMP is developed in Germany, the default time zone is set to `Europe/Berlin`. To make things easier when debugging things if you are not in Germany is to adjust the timezone like this. First, open up the MAMP specific `php.ini` file like this:

    nano /Applications/MAMP/bin/php/php5.6.10/conf/php.ini

Find this chunk of configuration code:

	; Defines the default timezone used by the date functions
	; Will be changed by MAMP to system timezone
	date.timezone = "Europe/Berlin"

Then adjust it like this; which in this case switches the timezone to `America/New_York`:

	; Defines the default timezone used by the date functions
	; Will be changed by MAMP to system timezone
	; date.timezone = "Europe/Berlin"
	date.timezone = "America/New_York"

With that done, save the file, restart MAMP and the timezone for things like PHP error logs will now be properly set to the `America/New_York` timezone.

***

### PHP MSSQL under MAMP 3.5.2 running PHP 5.6.10

Before anything else, make sure your local MAMP install’s binary paths are part of you default PATH. You would do this by adding the following paths to your main PATH in your `~/.bash_profile`:

* **MAMP 3.5.2 Main `bin/` Path**: `/Applications/MAMP/Library/bin`
* **MAMP 3.5.2 PHP `bin/` Path**: `/Applications/MAMP/bin/php/php5.6.10/bin`

#### Compile and Install FreeTDS

We need to install FreeTDS within the installed version of MAMP.

Before anything else, make sure to head to your desktop before anything else happens:

	cd ~/Desktop

Now use Curl to grab the latest stable version of FreeTDS like this:

	curl -O -L ftp://ftp.freetds.org/pub/freetds/stable/freetds-patched.tar.gz

Then decompress the FreeTDS source code archive:

	tar -xf freetds-patched.tar.gz

Now go into the decompressed source code directory:

	cd freetds-*

And run this `configure` command:

	./configure --prefix=/Applications/MAMP/Library --with-tdsver=7.4 --sysconfdir=/Applications/MAMP/conf/freetds

With that done, run `make`:

	make

And finally install FreeTDS into the installed version of MAMP:

	make install

#### Compile PHP

Now we will download the exact same version of PHP you will be running under MAMP so we can properly compile the MSSQL module. We will not be completely rebuilding PHP from source, but we need the full PHP source code to build the module against.

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

And finally with that done, run `make`:

	make

#### Compiling the MSSQL Module

With PHP now compiled, let’s now go into the MSSQL PHP extension directory:

	cd ext/mssql

We’re going to run `phpize` to prepare the MSSQL PHP extension of compiling:

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

	./configure --with-mssql=/Applications/MAMP/Library

And finally with that done, run `make` to compile the MSSQL PHP extension:

	make

#### Installing the MSSQL Module

If that runs successfully you have now successfully created a `mssql.so` PHP module that has been compiled against `php-5.6.10` and is ready to use with MAMP.

Copy that module over into PHP version specific directory in MAMP like this:

	cp modules/mssql.so /Applications/MAMP/bin/php/php5.6.10/lib/php/extensions/no-debug-non-zts-20131226/

Now adjust the PHP config file (`php.ini`) like this to get PHP to recognize it:

	echo "extension=mssql.so" >> /Applications/MAMP/bin/php/php5.6.10/conf/php.ini

Now start MAMP again and check the output of the PHP info page and `mssql` should be clearly listed there under installed components.

***

### PHP Oracle OCI8 under MAMP 4.0.6 running PHP 5.6.27

Notes here based on information gleaned from these two excellent blog entries:

* `https://www.thereluctantdeveloper.com/2014/04/adding-the-oracle-extension-oci8-to-mamp-pro-3os-x-109`
* `https://web.archive.org/web/20150811141206/http://caseymorford.com/2014/04/17/installing-oci8-with-mamp-pro/`

First, you need to get copies of the “Basic” and “SDK” archives for the Oracle Instant Client directly from Oracle. The archives I used were:

* `instantclient-basic-macos.x64-12.1.0.2.0.zip`
* `instantclient-sdk-macos.x64-12.1.0.2.0.zip`

Once you get those archives, create the directory structure they will live in here:

	sudo mkdir -p /opt/oracle/instantclient/

Now copy—or move—those ZIP archives into that directory and decompress them. They should both decompress into the a directory named `instantclient_12_1` which would make the full path:

	/opt/oracle/instantclient/instantclient_12_1/

With that done, go into that directory:

	cd /opt/oracle/instantclient/instantclient_12_1/

And then set these two symbolic links:

	sudo ln -s libclntsh.dylib.12.1 libclntsh.dylib
	sudo ln -s libocci.dylib.12.1 libocci.dylib

Now install the OCI 8 version that works with your version of PHP. Here is how it breaks down:

* **PHP 7**: `pecl install oci8`
* **PHP 5.2–5.6**: `pecl install oci8-2.0.12`
* **PHP 4.3.9-5.1**: `pecl install oci8-1.4.10`

So in this case, we are installing it for PHP version 5.6.27 so we will install version 2.0.12 using this command:

	pecl install oci8-2.0.12

During the install you might be asked to provide the `ORACLE_HOME` path via a message like this:

	Please provide the path to the ORACLE_HOME directory. Use 'instantclient,/path/to/instant/client/lib' if you're compiling with Oracle Instant Client [autodetect] :

We are not permanently setting `ORACLE_HOME` so just use this as the path:

	instantclient,/opt/oracle/instantclient/instantclient_12_1

After PECL does it’s thing, the `oci8.so` should be compiled and ready to go.

Now adjust the PHP config file (`php.ini`) like this to get PHP to recognize it:

	echo "extension=oci8.so" >> /Applications/MAMP/bin/php/php5.6.27/conf/php.ini

Now start MAMP again and check the output of the PHP info page and `oci8` should be clearly listed there under installed components.

***

*macOS - MAMP Related Items (c) by Jack Szwergold; written on October 7, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*