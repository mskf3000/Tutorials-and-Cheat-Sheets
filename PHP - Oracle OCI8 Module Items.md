## PHP - Oracle OCI8 Module Items

By Jack Szwergold

### PHP Oracle OCI8 under CentOS 6.8

#### Install the Oracle Instant Client Archives

Copy the Instant Client RPMs to the system you will be installing them on. Here are the SCP commands for CentOS 6.8 on my VirtualBox local development environment setup:

	scp ~/Desktop/oracle-instantclient12.2-* vagrant@sandbox-centos-68.local:.

SSH into the machine you just SCP’ed stuff to. Like the CentOS 6.8 sandbox:

	ssh vagrant@sandbox-centos-68.local

Now install the RPMs like this:

	sudo rpm -Uvh oracle-instantclient12.2-basic-12.2.0.1.0-1.x86_64.rpm
	sudo rpm -Uvh oracle-instantclient12.2-devel-12.2.0.1.0-1.x86_64.rpm

#### Compiling the OCI8 Module

If you have to, install the PHP Pear and PHP Devel packages. This is the command for CentOS 6.8 sandbox:

	sudo yum install php56w-pear php56w-devel

And you might need to run this command if you get this message, “WARNING: channel "pecl.php.net" has updated its protocols, use "pecl channel-update pecl.php.net" to update”:

	sudo pecl channel-update pecl.php.net

Now install the OCI8 version that works with your version of PHP. Here is how it breaks down:

* **PHP 7**: `sudo pecl install oci8`
* **PHP 5.2–5.6**: `sudo pecl install oci8-2.0.12`
* **PHP 4.3.9-5.1**: `sudo pecl install oci8-1.4.10`

So in this case, we are installing it for PHP version 5.6.10 so we will install version 2.0.12 using this command:

	sudo pecl install oci8-2.0.12

If you need to force a rebuild, use the `-f` option like this:

	sudo pecl install -f oci8-2.0.12

During the install you might be asked to provide the `ORACLE_HOME` path via a message like this:

	Please provide the path to the ORACLE_HOME directory. Use 'instantclient,/path/to/instant/client/lib' if you're compiling with Oracle Instant Client [autodetect] :

We are not permanently setting `ORACLE_HOME` so just use this as the path:

	instantclient,/usr/lib/oracle/19.6/client64/lib/

After PECL does it’s thing, the `oci8.so` should be compiled and ready to go.

#### Installing the OCI8 Module

Now adjust the PHP config file (`php.ini`) like this to get PHP to recognize it:

	sudo sh -c "printf '\n[OCI8]\nextension=oci8.so\n' >> /etc/php.ini"

Now start restart Apache and check the output of the PHP info page and `oci8` should be clearly listed there under installed components.

	sudo service httpd restart

***

### PHP Oracle OCI8 under CentOS 7

#### Install the Oracle Instant Client Archives

Copy the Instant Client RPMs to the system you will be installing them on. Here are the SCP commands for CentOS 7 on my VirtualBox local development environment setup:

	scp ~/Desktop/oracle-instantclient12.2-* vagrant@sandbox-centos-7.local:.

SSH into the machine you just SCP’ed stuff to. Like the CentOS 7 sandbox:

	ssh vagrant@sandbox-centos-7.local

Now install the RPMs like this:

	sudo rpm -Uvh oracle-instantclient12.2-basic-12.2.0.1.0-1.x86_64.rpm
	sudo rpm -Uvh oracle-instantclient12.2-devel-12.2.0.1.0-1.x86_64.rpm

#### Compiling the OCI8 Module

If you have to, install the PHP Pear and PHP Devel packages. This is the command for CentOS 7 sandbox:

	sudo yum install php-pear php-devel

And you might need to run this command if you get this message, “WARNING: channel "pecl.php.net" has updated its protocols, use "pecl channel-update pecl.php.net" to update”:

	sudo pecl channel-update pecl.php.net

Now install the OCI8 version that works with your version of PHP. Here is how it breaks down:

* **PHP 7**: `pecl install oci8`
* **PHP 5.2–5.6**: `pecl install oci8-2.0.12`
* **PHP 4.3.9-5.1**: `pecl install oci8-1.4.10`

So in this case, we are installing it for PHP version 5.6.10 so we will install version 2.0.12 using this command:

	sudo pecl install oci8-2.0.12

If you need to force a rebuild, use the `-f` option like this:

	sudo pecl install -f oci8-2.0.12

During the install you might be asked to provide the `ORACLE_HOME` path via a message like this:

	Please provide the path to the ORACLE_HOME directory. Use 'instantclient,/path/to/instant/client/lib' if you're compiling with Oracle Instant Client [autodetect] :

We are not permanently setting `ORACLE_HOME` so just use this as the path:

	instantclient,/usr/lib/oracle/12.2/client64/lib/

After PECL does it’s thing, the `oci8.so` should be compiled and ready to go.

#### Installing the OCI8 Module

Now adjust the PHP config file (`php.ini`) like this to get PHP to recognize it:

	sudo sh -c "printf '\n[OCI8]\nextension=oci8.so\n' >> /etc/php.ini"

Now start restart Apache and check the output of the PHP info page and `oci8` should be clearly listed there under installed components.

	sudo service httpd restart

***

### Dealing with some issues if they pop up.

For some Terminal connections — mainly on older server OS installs — running `pecl install -f oci8` and then responding `instantclient,/usr/lib/oracle/12.1/client64/lib/` to the path question won’t work. The workaround for that is to run an `echo` and a pipe (`|`) command like this:

	echo "instantclient,/usr/lib/oracle/12.1/client64/lib/" | pecl install -f oci8

And if you are installing this on some PHP setups, this error might come up after that `pecl install` kicks in:

	error: oci8_dtrace_gen.h: No such file or directory make: *** [oci8.lo] Error 1

In cases like that, you need to disable DTrace support to install OCI8 by opening up this file:

	sudo nano /usr/include/php/main/php_config.h

Look for this:

	/* Defined to 1 if PHP OCI8 DTrace support was enabled during configuration */
	#define HAVE_OCI8_DTRACE 1

And change it to this; basically commenting out that line:

	/* Defined to 1 if PHP OCI8 DTrace support was enabled during configuration */
	/* #define HAVE_OCI8_DTRACE 1 */

Then go back and run that `pecl install` command again.

***

*PHP - Oracle OCI8 Module Items (c) by Jack Szwergold; written on October 10, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*