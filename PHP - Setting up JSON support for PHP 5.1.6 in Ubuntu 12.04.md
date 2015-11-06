# PHP - Setting up JSON support for PHP 5.1.6 in Ubuntu 12.04

By Jack Szwergold, October 13, 2015

***

These were notes from an attempt to get JSON support included for a custom compiled version of PHP 5.1.6. Doesn’t seem to work as expected. Notes are here for reference just in case I need to revisit this process at some point.

***

### Get the Pear `json-1.2.1.tgz` archive.

First, install JSON via Pecl like this:

	sudo pecl install json

You might get a warning that reads like this:

     WARNING: "pear/json" is deprecated in favor of "channel://http://www.php.net/json/json"

Just ignore it. The warning is correct in that newer version of PHP have JSON support built-in, but we are dealing with adding JSON support to an ancient version of PHP so this is what we got to do.

### Finding where the Pear `json-1.2.1.tgz` archive was downloaded to.

Now the tricky part is finding the exact path the `json-1.2.1.tgz`. In some cases it will be installed in a directory like this:

	/build/buildd/php5-5.3.5/pear-build-download/

In others it will be installed in a directory like this:

    /build/php5-yIoy8f/php5-5.3.10/pear-build-download/

Note the `php5-yIoy8f/` directory name is a hash so it might change on each install. So it’s sometimes best to just run a `find` command like this to find the full path of the file:

    find /build -type f -name "json-1.2.1.tgz"

If you forget `find` syntax like I do sometimes, this combo command with output piped to `grep` works just as well:

    find /build | grep json-1.2.1.tgz

Or you can just update the `locate` database like this:

    sudo updatedb

And the just run this command:

    locate

Which would return something like this:

    /build/php5-yIoy8f/php5-5.3.10/pear-build-download/json-1.2.1.tgz

So just go into that directory like this and follow the rest of the steps:

    cd /build/php5-yIoy8f/php5-5.3.10/pear-build-download/

### Compiling and installing the extension.

Next, decompress the archive like this:

	sudo tar -xvf ./json-1.2.1.tgz

Now go into the decompressed directory:

	cd json-1.2.1

Run the `phpize` command on the code like this:

	sudo /opt/php516-gd/bin/phpize json

Run this `configure` command:

	sudo ./configure
	
Once the `configure` process completes, run `make`:

	sudo make

Finally install it by running `sudo make install`:

	sudo make install

***

*PHP - Setting up JSON support for PHP 5.1.6 in Ubuntu 12.04 (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
