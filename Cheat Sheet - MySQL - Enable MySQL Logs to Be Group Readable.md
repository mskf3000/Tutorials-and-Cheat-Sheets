# Cheat Sheet - MySQL - Enable MySQL Logs to Be Group Readable

By Jack Szwergold, September 24, 2015

This procedure allows you to set MySQL logs to be group readable which can be useful for debugging scenarios. Still a slight security risk but if this is a needed for your usage, this is how you do it:

***

#### Adjust the ownership and permissions on the MySQL log files.

First, change the group ownership of the main MySQL logs directory. Check the directory permssions like this:

	sudo ls -la /var/log/mysql

And change the group ownership like this:

	sudo chgrp www-readwrite -R /var/log/mysql

Next, change the permissions associated to the main MySQL logs directory and related files like this:

	sudo chmod 754 /var/log/mysql
	
Then change the MySQL log file permissions like this:

	sudo chmod 640 -R /var/log/mysql/*.log*

Now follow the logs as a regular user—without using `sudo`—to test the setup out:

	tail -f -n 200 /var/log/mysql/mysql.log
	tail -f -n 200 /var/log/mysql/error.log

#### Adjust the MySQL daemon ownership and permissions in the MySQL config file.

Edit the main MySQL config file to set the group permissions to match everything else. First open up the main MySQL config file like this:

	sudo nano /etc/mysql/my.cnf

Now look for the `[mysqld]` configuration area. It should only have a `user` setting like this:

	[mysqld]
	#
	# * Basic Settings
	#
	user            = mysql

Adjust it so it has a `group` setting and that setting is set to `www-readwrite`:

	[mysqld]
	#
	# * Basic Settings
	#
	user            = mysql
	group           = www-readwrite

#### Adjust the MySQL `logrotate` script.

Adjust the `logrotate` script to make sure permissions persist when logs are rotated:

	sudo nano /etc/logrotate.d/mysql-server

Change this line:

    create 640 mysql adm

To this:

    create 640 mysql www-readwrite

#### An example of what log ownership and permissions look like before the adjustment.

	ls -la /var/log/mysql/
	
	drwxr-s---  2 mysql adm   4096 2013-01-07 01:37 .
	drwxr-xr-x 10 root  root  4096 2013-01-07 01:37 ..
	-rw-rw----  1 mysql adm  35400 2012-12-10 22:12 error.log
	-rw-r-----  1 mysql adm    437 2013-01-07 03:00 mysql-slow.log
	-rw-r-----  1 mysql adm    437 2013-01-06 03:00 mysql-slow.log.1
	-rw-r-----  1 mysql adm    361 2013-01-05 15:00 mysql-slow.log.2.gz
	-rw-r-----  1 mysql adm    362 2013-01-04 15:00 mysql-slow.log.3.gz
	-rw-r-----  1 mysql adm    332 2013-01-03 03:00 mysql-slow.log.4.gz
	-rw-r-----  1 mysql adm    334 2013-01-02 03:00 mysql-slow.log.5.gz
	-rw-r-----  1 mysql adm    333 2013-01-01 03:00 mysql-slow.log.6.gz
	-rw-r-----  1 mysql adm    334 2012-12-31 03:00 mysql-slow.log.7.gz

#### An example of what log ownership and permissions look like after the adjustment.

	ls -la /var/log/mysql/
	
	drwxr-sr--  2 mysql www-readwrite   4096 2013-01-07 11:33 .
	drwxr-xr-x 11 root  root            4096 2013-01-07 03:01 ..
	-rw-r-----  1 mysql www-readwrite   2796 2013-01-07 11:33 error.log
	-rw-r-----  1 mysql www-readwrite 111106 2013-01-07 11:48 mysql.log
	-rw-r-----  1 mysql www-readwrite   2002 2013-01-07 11:33 mysql-slow.log
	-rw-r-----  1 mysql www-readwrite    182 2013-01-06 03:01 mysql-slow.log.1
	-rw-r-----  1 mysql www-readwrite    159 2013-01-05 03:01 mysql-slow.log.2.gz
	-rw-r-----  1 mysql www-readwrite    159 2013-01-04 03:01 mysql-slow.log.3.gz
	-rw-r-----  1 mysql www-readwrite    159 2013-01-03 03:01 mysql-slow.log.4.gz
	-rw-r-----  1 mysql www-readwrite    159 2013-01-02 03:00 mysql-slow.log.5.gz
	-rw-r-----  1 mysql www-readwrite    159 2013-01-01 03:21 mysql-slow.log.6.gz
	-rw-r-----  1 mysql www-readwrite    159 2012-12-31 03:01 mysql-slow.log.7.gz

***

*Cheat Sheet - Redis - Installing Redis from Source via a Custom Debian Package Build on Ubuntu (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

