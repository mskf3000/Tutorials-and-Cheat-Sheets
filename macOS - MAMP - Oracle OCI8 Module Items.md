## macOS - MAMP - Oracle OCI8 Module Items

By Jack Szwergold

### PHP Oracle OCI8 under MAMP 3.5.2 running PHP 5.6.10

Notes here based on information gleaned from these two excellent blog entries:

* `https://www.thereluctantdeveloper.com/2014/04/adding-the-oracle-extension-oci8-to-mamp-pro-3os-x-109`
* `https://web.archive.org/web/20150811141206/http://caseymorford.com/2014/04/17/installing-oci8-with-mamp-pro/`

***

Before anything else, make sure your local MAMP install’s binary paths are part of you default PATH. You would do this by adding the following paths to your main PATH in your `~/.bash_profile`:

* **MAMP 3.5.2 Main `bin/` Path**: `/Applications/MAMP/Library/bin`
* **MAMP 3.5.2 PHP `bin/` Path**: `/Applications/MAMP/bin/php/php5.6.10/bin`

***

First, you need to get copies of the “Basic” and “SDK” archives for the Oracle Instant Client directly from Oracle. The archives I used were:

* `instantclient-basic-macos.x64-12.1.0.2.0.zip`
* `instantclient-sdk-macos.x64-12.1.0.2.0.zip`

Once you get those archives, create the directory structure they will live in here:

	sudo mkdir -p /opt/oracle/instantclient/

Now copy—or move—those ZIP archives into that directory and decompress them like this. First, go into the parent Instant Client directory like this:

	cd /opt/oracle/instantclient/

Now unZip the archives like this:

	sudo unzip instantclient-basic-macos.x64-12.1.0.2.0.zip
	sudo unzip instantclient-sdk-macos.x64-12.1.0.2.0.zip

And when that’s done, cleanup by deleting the Zip archives:

	sudo rm *.zip

They should both decompress into the a directory named `instantclient_12_1` which would make the full path:

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

So in this case, we are installing it for PHP version 5.6.10 so we will install version 2.0.12 using this command:

	pecl install oci8-2.0.12

During the install you might be asked to provide the `ORACLE_HOME` path via a message like this:

	Please provide the path to the ORACLE_HOME directory. Use 'instantclient,/path/to/instant/client/lib' if you're compiling with Oracle Instant Client [autodetect] :

We are not permanently setting `ORACLE_HOME` so just use this as the path:

	instantclient,/opt/oracle/instantclient/instantclient_12_1

After PECL does it’s thing, the `oci8.so` should be compiled and ready to go.

Now adjust the PHP config file (`php.ini`) like this to get PHP to recognize it:

	echo "extension=oci8.so" >> /Applications/MAMP/bin/php/php5.6.10/conf/php.ini

Now start MAMP again and check the output of the PHP info page and `oci8` should be clearly listed there under installed components.

***

*macOS - MAMP - Oracle OCI8 Module Items (c) by Jack Szwergold; written on August 17, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*