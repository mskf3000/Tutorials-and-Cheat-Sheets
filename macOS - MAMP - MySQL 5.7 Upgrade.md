## macOS - MAMP - MySQL 5.7 Upgrade

By Jack Szwergold

Get the macOS binaries for MySQL 5.7:

	curl -OL https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.22-macos10.13-x86_64.tar.gz

Decompress it like this:

	tar xfvz mysql-5.7*

Copy that stuff into MAMP via Rsync like this:

	sudo rsync -arv --progress mysql-5.7.*/bin/* /Applications/MAMP/Library/bin/ --exclude=mysqld_multi --exclude=mysqld_safe
	sudo rsync -arv --progress mysql-5.7.*/share/* /Applications/MAMP/Library/share/

Copy the database directory like this; is this needed? Why not just rename it?

	sudo cp -r /Applications/MAMP/db/mysql56 /Applications/MAMP/db/mysql57

Get rid of MySQL specific binaries for upgrade:

	sudo rm -fr /Applications/MAMP/db/mysql57/mysql/innodb_*
	sudo rm -fr /Applications/MAMP/db/mysql57/mysql/slave_*

Be sure to change the owner of the DB directory to you:

	sudo chown -R ${USER}:admin /Applications/MAMP/db/mysql57

Change this script to point to MySQL 5.7:

	sed -i.bak 's/mysql56/mysql57/g' /Applications/MAMP/Library/bin/mysqld_safe
	
Now start MAMP via the application and run this command to upgrade the databases:
	
	/Applications/MAMP/Library/bin/mysql_upgrade --user=root --password=root --port=3306 --socket=/Applications/MAMP/tmp/mysql/mysql.sock --force

And run this command to get the `mysql.sock` properly set for MAMP:

	/Applications/MAMP/Library/bin/mysql_config_editor --verbose set --socket=/Applications/MAMP/tmp/mysql/mysql.sock

***

*macOS - MAMP - MySQL 5.7 Upgrade (c) by Jack Szwergold; written on April 26, 2018. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*