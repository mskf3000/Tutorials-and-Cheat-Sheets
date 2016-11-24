## PHP

By Jack Szwergold

### Disable `expose_php` in PHP.

Check if `expose_php` is on like this:

    http://example.com/some_kind_of.php?=PHPE9568F35-D428-11d2-A769-00AA001ACF42

Edit the PHP ini file.

	sudo nano /etc/php5/apache2/php.ini

And disable `expose_php` like this:

	expose_php = Off

Save the file and reload Apache to refresh the PHP settings:

	sudo service apache2 reload

### Dealing with PHP session ownership issues.

If the group has been changed, permissions on the PHP session directory need to be changed too:

    sudo chgrp www-readwrite -R /var/lib/php/session

### Resolving PHP session creation issues.

Sometimes, the ownership of the parent sessions directory are mucked up. Check it like this:

	sudo ls -la /var/lib/php5

The output might look like this; note how the parent directory is owned by `root` and the group is `root`; Apache and PHP can’t write to a directory like this:

	drwx-wx-wt  2 root     root     4096 2012-10-12 14:49 .
	drwxr-xr-x 47 root     root     4096 2012-09-21 18:13 ..
	-rw-------  1 www-data www-data    0 2012-10-12 14:49 sess_8pjm82964slq3jd4aeh9n9n324
	-rw-------  1 www-data www-data    0 2012-10-12 14:45 sess_9qjtv5iniun2jubbttc97luvb1
	-rw-------  1 www-data www-data    0 2012-10-12 14:49 sess_ea9a4qv65u7mic3pestn1n6lf0
	-rw-------  1 www-data www-data  290 2012-10-12 14:49 sess_q0fra5mt5f3h9o5hrh040gddn3

To solve the issue change the owner and group of the directory to be the Apache user. Which in this case—on an Ubuntu system—would be `www-data`:

	sudo chown www-data:www-data /var/lib/php5

Then check the directory again:

	sudo ls -la /var/lib/php5

The output should be something like this:

	total 12
	drwx-wx-wt  2 www-data www-data 4096 2012-10-12 14:55 .
	drwxr-xr-x 47 root     root     4096 2012-09-21 18:13 ..
	-rw-------  1 www-data www-data    0 2012-10-12 14:49 sess_8pjm82964slq3jd4aeh9n9n324
	-rw-------  1 www-data www-data    0 2012-10-12 14:45 sess_9qjtv5iniun2jubbttc97luvb1
	-rw-------  1 www-data www-data    0 2012-10-12 14:49 sess_ea9a4qv65u7mic3pestn1n6lf0
	-rw-------  1 www-data www-data    0 2012-10-12 14:55 sess_j8nhf7q9ojrbu4sf4d5cf3ajd2
	-rw-------  1 www-data www-data  290 2012-10-12 14:49 sess_q0fra5mt5f3h9o5hrh040gddn3

### Enable PHP error logging for the command line interface.

First, create the PHP CLI (command line interface) `php_errors.log` log file manually like this:

    sudo touch /var/log/php_errors.log

Then change owner of the file to `root` and the group to `www-readwrite` like this:

    sudo chown root:www-readwrite /var/log/php_errors.log

And then change the permissions of the file to this:

    sudo chmod 664 /var/log/php_errors.log

Next, go into `/etc/php5/cli/php.ini` to adjust the default Ubuntu 12.04 settings to match this new log file:

	sudo nano /etc/php5/cli/php.ini

Make sure the `log_errors` setting is enabled:

	log_errors = On

But to allow logging make sure to change the `error_log` value to match the new file like this:

	error_log = /var/log/php_errors.log

#### Setup a `logrotate.d` script.

Now that should be it as far as enabling PHP CLI logging goes. But since you don’t want logs to run out of control you should set up a `logrotate.d` script for the `php_errors.log`. Create a file called `php-cli` in `/etc/logrotate.d/` like this:

	sudo nano /etc/logrotate.d/php-cli

And place the contents of this `logrotate.d` script in there:

	/var/log/php_errors.log {
	        weekly
	        missingok
	        rotate 13
	        compress
	        delaycompress
	        copytruncate
	        notifempty
	        create 664 root www-readwrite
	        sharedscripts
	}

#### Testing the setup.

Send a test error message to the log.

    php -r "error_log('This is an error test that we hope works.');"

To confirm that works, check the error log.

    tail -n 10 /var/log/php_errors.log

There should be a line in there like this.

	[23-Jul-2014 16:04:56 UTC] This is an error test that we hope works.

### Disable the PHP OPCache in PHP 5.5.

Caching is nice, but it can drive you nuts in many circumstances; development or even production environments. You can disable it like this. First open up your main PHP config file:

    sudo nano /etc/php5/apache2/php.ini

Find this chunk of config code:

	[opcache]
	; Determines if Zend OPCache is enabled
	;opcache.enable=0

And uncomment that `; opcache.enable=0` line so it looks like this:

	[opcache]
	; Determines if Zend OPCache is enabled
	opcache.enable=0

Now restart Apache to have those new PHP settings take effect and you should be good to go.

### Installing PHP MBString (`php-mbstring`) on Red Hat

In case you are working on a Red Hat Enterprise Linux setup that doesn’t have the “optional” RPM source enabled and you need to get `php-mbstring` installed, just get the RPM directly like this:

	wget http://mirror.centos.org/centos/6/updates/x86_64/Packages/php-mbstring-5.3.3-48.el6_8.x86_64.rpm

And now just install it directly from the RPM like this:

	sudo yum localinstall php-mbstring-5.3.3-48.el6_8.x86_64.rpm

***

*PHP (c) by Jack Szwergold; written on October 5, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
