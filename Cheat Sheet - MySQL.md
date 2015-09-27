# Cheat Sheet - MySQL

By Jack Szwergold, September 24, 2015

***

#### Sundry MySQL items.

Get MySQL version number:

    mysqladmin -V

Start, stop and control MySQL on an Ubuntu/Debian system:

	sudo service mysql status
	sudo service mysql start
	sudo service mysql stop
	sudo service mysql restart
	sudo service mysql reload
	sudo service mysql force-reload

Simple MySQL status:

    mysqladmin status

Extended MySQL status:

	mysqladmin extended

Get the MySQL configuration:

	mysql_config

Get the IDs of MySQL processes on a running system:

	ps ax | grep mysql

Determine which `my.cnf` is being used:

	mysql --help | grep -A 1 "Default options"

Determine what startup flags MySQL starts with:

	mysql --print-defaults

Edit the config file:

	sudo nano /etc/mysql/my.cnf

Location of other, example config files:

	ls -la /usr/local/mysql/share/mysql/*.cnf

Follow the MySQL error log:

	sudo tail -f -n 200 /var/log/mysql/error.log

Follow the MySQL slow queries log:

	sudo tail -f -n 200 /var/log/mysql/mysql-slow.log

Login as root:

    mysql -u root -p

#### Securing an initial MySQL installation by removing test items.

If your version of MySQL supports it, just run `mysql_secure_installation` to secure the installation and get rid of test databases and users:

	sudo mysql_secure_installation

But if your MySQL version doesn’t support `mysql_secure_installation`, then just run these commands within MySQL as an admin:

	DROP DATABASE test;
	
	SELECT db from mysql.db;
	
	DELETE FROM mysql.db WHERE db = 'test';
	DELETE FROM mysql.db WHERE db = 'test\_%';
	
	DELETE FROM user WHERE user = '';

### Importing and exporting MySQL database dumps.

Import a MySQL dump into MySQL like this:

	mysql -u [user] -p [password] [database] <[path_to_database].sql

How to import a GZipped MySQL dump straight into MySQL like this:

	gzcat [name of mysql dump].gz | mysql -u [user] -p [password] [database]

Cool—but in my opinion risky—method of dumping from one database and importing into another in one command:

	mysqldump -u [user] -p [password] [database] | mysql -u[user] -p[password] [database]

### Fix MySQL InnoDB oddities.

#### Basic InnoDB cleanup workflow.

First, understand this basic workflow or else you will damage your MySQL InnoDB related database:

1. Backup the InnoDB databases.
2. Drop the InnoDB databases.
3. Stop the MySQL server.
4. Remove InnoDB related files on the filesystem.
5. Start the MySQL server.
6. Restore the backups of the InnoDB databases.

#### Remove InnoDB related files on the filesystem.

The key item described here is the item in step 4 in the basic workflow outlined above. **If you have no idea what any of the other steps mean, you are in over you head and should not procede any further.** You will most likely damage your MySQL InnoDB related databases if you don’t know any better.

That said, if you know your way around MySQL servers and know what you’re doing here’s how to remove InnoDB related files on the filesystem.

First, do a simple check for the InnoDB stuff:

    sudo ls -lah /var/lib/mysql/

Remove the InnoDB related binary file stuff. It will be rebuilt when MySQL restarts:

	sudo rm /var/lib/mysql/ibdata1
	sudo rm /var/lib/mysql/ib_logfile0
	sudo rm /var/lib/mysql/ib_logfile1

### Different methods used to start MySQL in Mac OS X.

#### With a “Startup Item.”

In Mac OS X, if you have installed the “Startup Item,” use this command:

	sudo /Library/StartupItems/MySQLCOM/MySQLCOM start

Enter your password if prompted to do so. Then press “Ctrl+D” or type “EXIT” to exit the shell.

#### Manually without a “Startup Item.”

If you’re not using the “Startup Item” in Mac OS X, enter the following command sequence:

	cd /usr/local/mysql
	sudo ./bin/mysqld_safe

Enter your password if prompted to do so. Then press “Ctrl+Z.” When you are in the shell, type in `bg` like this:

	shell> bg

Then press “Ctrl+D” or type “EXIT” to exit the shell.

### Reset the MySQL `root` password.

Note that this method is regarded as an unsafe way of resetting the password. However it works.

Stop the MySQL Server:

	sudo service mysql stop

Start the mysqld configuration in Ubuntu/Debian:

    sudo mysqld --skip-grant-tables &

Start the mysqld configuration in RedHat/CentOS:

    sudo /usr/bin/mysqld_safe --skip-grant-tables &

Login to MySQL as root:

    mysql -u root mysql

Replace `YOURNEWPASSWORD` with your new password:

	UPDATE user SET Password=PASSWORD('YOURNEWPASSWORD') WHERE user='root'; FLUSH PRIVILEGES; exit;

### MySQL Tuning Primer

	curl -O https://launchpadlibrarian.net/78745738/tuning-primer.sh
	
	wget https://launchpadlibrarian.net/78745738/tuning-primer.sh
	
	chmod u+x tuning-primer.sh
	
	./tuning-primer.sh

#### Changing a user password from the command line.

    mysqladmin -u root -p'old password' password 'new password'


#### InnoDB adjustment we’re doing this to better control InnoDB size in the filesystem.

	sudo nano /etc/mysql/my.cnf

Find `skip-external-locking` and add this setting right after it:

    innodb_file_per_table


#### Here’s a MySQL script to list out the entire databases size

One script to do some calcuations on MySQL database sizes:

	SELECT table_schema "Data Base Name", SUM( data_length + index_length) / 1024 / 1024
	"Data Base Size in MB" FROM information_schema.TABLES GROUP BY table_schema ;

Another script to do some calcuations on MySQL database sizes:

	SELECT TABLE_NAME, table_rows, data_length, index_length,
	round(((data_length + index_length) / 1024 / 1024),2) "Size in MB"
	FROM information_schema.TABLES WHERE table_schema = "schema_name";

#### How to update and replace items in a MySQL database table.

	UPDATE a_table = REPLACE(a_table.field_in_table, "Original Value", "New Value");

#### Check for all users on a system.

    SELECT user, host FROM `mysql`.`user`;

#### Exporting data as a CSV

	SELECT order_id,product_name,qty
	FROM orders
	INTO OUTFILE '/tmp/orders.csv'
	FIELDS TERMINATED BY ','
	ENCLOSED BY '"'
	LINES TERMINATED BY '\n'

## MAMP MySQL specific stuff.

#### Location of `my.cnf` in MAMP 1.9.

Regular MAMP doesn’t have one by default (although apparantly MAMP Pro does) but you can either create one yourself at, or copy one of the `my-*.cnf` files from `/Applications/MAMP/Library/support-files/` as:

	/Applications/MAMP/Library/my.cnf

And just copy it to the new location like this:

	cp /Applications/MAMP/Library/support-files/my-medium.cnf /Applications/MAMP/Library/my.cnf

#### Location of `my.cnf` in MAMP 2.0.

And for version MAMP 2.0 (and above) the location of the MySQL config file would be here:

	/Applications/MAMP/conf/my.cnf

And just copy it to the new location like this:

	cp /Applications/MAMP/Library/support-files/my-medium.cnf /Applications/MAMP/conf/my.cnf

***

*Cheat Sheet - MySQL (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

