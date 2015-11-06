# MySQL

By Jack Szwergold, September 24, 2015

***

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

#### Managing the MySQL `init.d` scripts.

By default, MySQL should get an `init.d` installed. But if somehow it didn’t, run this `update-rc.d` command:

	sudo update-rc.d mysql defaults

And if for some reason you are running a server where MySQL is not needed, you can simply disable it from coming up on boot like this:

	sudo update-rc.d -f mysql remove

The nice thing about this is it disables the MySQL server but the MySQL client is still installed on the system if you need to remotely connect into something somewhere. And you can even start up the MySQL server manually if needed.

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

### Fix MySQL `mysqld.pid` and `mysqld.sock` items not being set.

Even though the locations for `mysqld.pid` and `mysqld.sock` are set, the directory doesn’t exist so those files are never created. This will fix that:

First create the `mysqld/` directory in `/var/run/` like this:

	sudo mkdir -p /var/run/mysqld/

Then create a `mysqld.pid` file with `touch`:
	
	sudo touch /var/run/mysqld/mysqld.pid

Next create a `mysqld.sock` file with `touch`:
	
	sudo touch /var/run/mysqld/mysqld.sock

Now change the ownerships of the directory and files to `mysql:mysql` like this:

	sudo chown mysql:mysql -R /var/run/mysqld

And restart MySQL like this to get those files set and created:

    sudo service mysql restart

### MySQL Tuning Primer

Grab a copy of the `tuning-primer.sh` script:

	curl -O -L https://launchpadlibrarian.net/78745738/tuning-primer.sh

Set user executable permissions like this:

	chmod u+x tuning-primer.sh

And run the script like this:

	./tuning-primer.sh

#### Changing a user password from the command line.

    mysqladmin -u root -p'old password' password 'new password'


#### InnoDB adjustment we’re doing this to better control InnoDB size in the filesystem.

	sudo nano /etc/mysql/my.cnf

Find `skip-external-locking` and add this setting right after it:

    innodb_file_per_table


#### Here’s a MySQL script to list out the entire databases size

One script to do some calculations on MySQL database sizes:

	SELECT table_schema "Data Base Name", SUM( data_length + index_length) / 1024 / 1024
	"Data Base Size in MB" FROM information_schema.TABLES GROUP BY table_schema ;

Another script to do some calculations on MySQL database sizes:

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

### MAMP MySQL specific stuff.

#### Location of `my.cnf` in MAMP 1.9.

Regular MAMP doesn’t have one by default (although apparently MAMP Pro does) but you can either create one yourself at, or copy one of the `my-*.cnf` files from `/Applications/MAMP/Library/support-files/` as:

	/Applications/MAMP/Library/my.cnf

And just copy it to the new location like this:

	cp /Applications/MAMP/Library/support-files/my-medium.cnf /Applications/MAMP/Library/my.cnf

#### Location of `my.cnf` in MAMP 2.0.

And for version MAMP 2.0 (and above) the location of the MySQL config file would be here:

	/Applications/MAMP/conf/my.cnf

And just copy it to the new location like this:

	cp /Applications/MAMP/Library/support-files/my-medium.cnf /Applications/MAMP/conf/my.cnf

### Setting up a user and related grants in MySQL.

#### Creating a user and setting the grants.

Drop a user:

    DROP USER 'username'@'localhost';

Create a user:

    CREATE USER 'username'@'localhost' IDENTIFIED BY 'aPassW0rd';

Set grants for a user:

	GRANT USAGE ON `some_database`.* TO 'username'@'localhost' IDENTIFIED BY 'username';
	GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER ON `some_database`.* TO 'username'@'localhost';

Set a user password:

    SET PASSWORD FOR 'username'@'localhost' = PASSWORD('aPassW0rd');

Flushing privileges reloads the grants tables so they can take effect:

    FLUSH PRIVILEGES;

#### Various commands for user and database setup.

Drop a database:

    DROP DATABASE `some_database`;

Create a user:

    CREATE DATABASE `some_database`;

This grants a user all privileges. Only do this for debugging and if you have no choice:

    GRANT ALL PRIVILEGES ON `some_database`.* TO 'username'@'localhost';

Show grants connected to a user:

    SHOW GRANTS FOR 'username'@'localhost';

Revokes grants for a user:

    REVOKE SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, FILE, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER ON `some_database`.* FROM 'username'@'localhost';

Flushing privileges reloads the grants tables so they can take effect:

    FLUSH PRIVILEGES;

### Setting MySQL as UTF8 on all connections.

Change the collation a specific database:

	ALTER DATABASE [name of your database] CHARACTER SET utf8;

Change the collation a specific table in a database:

	ALTER TABLE [name of your table] CONVERT TO CHARACTER SET utf8;

Change the collation a specific column in a table in a database:

	ALTER TABLE [name of your table] MODIFY [name of your column] [other settings] CHARACTER SET utf8 COLLATE utf8_general_ci;

Export the current database, create a new database with this command and then reimport the data:

	CREATE DATABASE [name of your database] CHARACTER SET utf8 COLLATE utf8_general_ci;

To make a permanent change to the MySQL install on the machine giving you an issue, go and edit `my.cnf`. The following would set the whole chain to UTF-8:

	[client]
	default-character-set=utf8
	
	[mysql]
	default-character-set=utf8
	
	[mysqld]
	collation-server = utf8_unicode_ci
	init-connect='SET NAMES utf8'
	character-set-server = utf8

In some cases you might need to use this for `init-connect` instead of the simple `SET NAMES utf8` value shown above:

    init-connect='SET collation_connection = utf8_general_ci; SET NAMES utf8;'

***

*MySQL (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

