## macOS - MAMP - MySQL 5.7 Upgrade

By Jack Szwergold

First, get a copy of the macOS binaries for MySQL 5.7; note that as of me posting this answer MySQL 5.7.22 is the current version so adjust this URL to whatever new version you might want to use:

	curl -OL https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.22-macos10.13-x86_64.tar.gz

Decompress it like this:

	tar xfvz mysql-5.7*

Copy the `bin/` and `share/` stuff into MAMP via Rsync like this:

	sudo rsync -arv --progress mysql-5.7.*/bin/* /Applications/MAMP/Library/bin/ --exclude=mysqld_multi --exclude=mysqld_safe
	sudo rsync -arv --progress mysql-5.7.*/share/* /Applications/MAMP/Library/share/

Now if you have existing databases, you can copy them like this; the `mysql56` directory is temporarily needed during the upgrade but can be discarded after the rest of this is done:

	sudo cp -r /Applications/MAMP/db/mysql56 /Applications/MAMP/db/mysql57

Once that is done, get rid of MySQL database specific binaries like this for upgrade:

	sudo rm -rf /Applications/MAMP/db/mysql57/mysql/innodb_*
	sudo rm -rf /Applications/MAMP/db/mysql57/mysql/slave_*

And instead of changing permissions to `o+rw` just change the owner of the DB directory to your current user; this matches how MAMP installs this stuff:

	sudo chown -R ${USER}:admin /Applications/MAMP/db/mysql57

Now run this Sed command to adjust the `mysqld_safe` script to point to the new MySQL 5.7 path; you could probably just open this file up in a text editor and change all instances of `mysql56` to `mysql57`as well:

	sed -i.bak 's/mysql56/mysql57/g' /Applications/MAMP/Library/bin/mysqld_safe

Finally, if you use MAMP and set a `my.cnf` file, that should be set in `/Applications/MAMP/conf/my.cnf`… But by doing this upgrade, the default search path of the `my.cnf` in MAMP will be `/usr/local/mysql/etc/` instead of the expected `/Applications/MAMP/conf/` since that is where the new binary expects it to be set. Clearly we’re not going to recompile MySQL at this point so the cleanest/simplest thing to do to make your MAMP setup truly portable again is to change this line in the `startMysql.sh` from this:

    /Applications/MAMP/Library/bin/mysqld_safe --port=8889 --socket=/Applications/MAMP/tmp/mysql/mysql.sock --pid-file=/Applications/MAMP/tmp/mysql/mysql.pid --log-error=/Applications/MAMP/logs/mysql_error_log &

To this; note we are adding the `--defaults-extra-file=` option before all the otgers:

    /Applications/MAMP/Library/bin/mysqld_safe --defaults-extra-file=/Applications/MAMP/conf/my.cnf --port=8889 --socket=/Applications/MAMP/tmp/mysql/mysql.sock --pid-file=/Applications/MAMP/tmp/mysql/mysql.pid --log-error=/Applications/MAMP/logs/mysql_error_log &

With all of that command line work done, launch MAMP via the application, start the MySQL and Apache servers and then drop back into the command like to run this command to upgrade the databases:
	
	/Applications/MAMP/Library/bin/mysql_upgrade --user=root --password=root --port=3306 --socket=/Applications/MAMP/tmp/mysql/mysql.sock --force

And finally run this command to get the `mysql.sock` properly set for MAMP path instead of that `/tmp/mysql.sock` path:

	/Applications/MAMP/Library/bin/mysql_config_editor --verbose set --socket=/Applications/MAMP/tmp/mysql/mysql.sock

When this is all done, and you have confirmed MySQL is running as expected, just toss the old MySQL 5.6 directory like this:

	sudo rm -rf /Applications/MAMP/db/mysql56

With all that done you should all be set to cleanly use MySQL 5.7 under MAMP 4.4.1.

***

*macOS - MAMP - MySQL 5.7 Upgrade (c) by Jack Szwergold; written on April 26, 2018. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*