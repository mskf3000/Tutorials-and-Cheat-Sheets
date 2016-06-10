## Mac OS X - Upgrading the PHP Pear Library in MAMP

By Jack Szwergold, October 7, 2015

### Setting your MAMP binary path in your shell.

#### Set the MAMP PHP 5.6.10 version of Pear as part of your user PATH.

Add it to the beginning of your `PATH` like this. First open up your user’s `.bash_profile` like this:

    bbedit ~/.bash_profile

Then add lines like this. This is my preferred method of managing `PATH` values. If you have a different method of adjust your `PATH` value feel free to adapt:

    export MAMP_BIN="/Applications/MAMP/Library/bin"
    export MAMP_PHP="/Applications/MAMP/bin/php/php5.6.10/bin"

    export PATH=$MAMP_BIN:$MAMP_PHP:/usr/local/git/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/usr/local/sbin

Save the `.bash_profile` and reload it like this:

	source ~/.bash_profile

### Fixing the Pear configuration file.

It’s worth noting for now that apparently the Pear configuration file `pear.conf` in the PHP 5.6.10 setup can just be deleted to fix the problem. But haven’t tested it for the other versions of PHP in MAMP.

#### Fixing the Pear configuration for PHP 5.6.10.

The default Pear configuration file installed by MAMP has a small typo. So it needs to be fixed before anything happens. First open the file like this:

    bbedit /Applications/MAMP/bin/php/php5.6.10/conf/pear.conf

The bad setup:

    "php_dir";s:44:"/Applications/MAMP/bin/php/php5.6.10/lib/php";

The good setup; note the trailing slash:

    "php_dir";s:44:"/Applications/MAMP/bin/php/php5.6.10/lib/php/";

Or just delete the Pear configuration file:

    rm /Applications/MAMP/bin/php/php5.6.10/conf/pear.conf

### Updating the actual Pear library.

This example uses the PHP 5.6.10 version of this process. The overall concept is the same for other versions, just note the path differences when implementing.

Do a `which pear` to see which version of Pear is being used. It should match the `bin` path set in the profile such as:

    /Applications/MAMP/bin/php/php5.6.10/bin

Which should return:

    /Applications/MAMP/bin/php/php5.6.10/bin/pear

    which pear

Do a dry run of `pear remote-list` to see what it gives you:

    pear remote-list

It might need to have `channel-update pear.php.net` set:

    pear channel-update pear.php.net

Once that is done, do another dry run of `pear remote-list`:

	pear remote-list
	
Should now look something like this:

	Channel pear.php.net Available packages:
	========================================
	Package                                   Version
	Auth_HTTP                                 2.1.8
	Auth                                      1.6.4
	Auth_SASL                                 1.0.6

Great! Now pipe that 'pear remote-list' into a file:

    pear remote-list | awk '{print $1}' > pear-list

Open that file:

    nano pear-list

And remove the stuff at the top of the file so it looks like this:

	Channel pear.php.net Available packages:
	========================================
	Package                                   Version

Now pipe that `pear-list` via `xargs` into `pear install` and wait:

	cat pear-list | xargs -n 1 pear install

***

*Mac OS X - Upgrading the PHP Pear Library in MAMP (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

