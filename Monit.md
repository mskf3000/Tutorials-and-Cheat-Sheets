## Monit

By Jack Szwergold

### Getting Monit installed and other basics.

First, install Monit via `aptitude` like this:

    sudo aptitude install monit

Controls Monit startup; don’t need to adjust for now:

    sudo nano /etc/default/monit

Main config for Monit; broad adjustments happen here:

    sudo nano /etc/monit/monitrc

Adjust the default daemon values to check services every 60 seconds with a start delay of 120:

	set daemon 60
	with start delay 60

Then find the `mailserver` area of `monitrc` and add the following line. Postfix or an SMTP needs to be active for this to work:

    set mailserver localhost

Create the `conf.d` monit directory if it doesn’t exist:

	sudo mkdir -p /etc/monit/conf.d

Follow the Monit log to see it in action:

    sudo tail -f -n 200 /var/log/monit.log

If a service becomes “unmonitored” then just remove the Monit state file like this:

    sudo rm /var/lib/monit/state

Restart Monit and all should be good:

    sudo service monit restart

### Ubuntu 12.04: Create a custom Monit Apache status monitoring rule.

Check to see if the `apache2.pid` file exists like this:

    ls -la /var/run/apache2.pid

Now create the actual Apache monitoring rule for Monit:

    sudo nano /etc/monit/conf.d/apache2.conf

One type of Apache monitoring rule:

	check process apache with pidfile /var/run/apache2.pid
      start "/usr/sbin/service apache2 start"
      stop  "/usr/sbin/service apache2 stop"
      if failed host 127.0.0.1 port 80
        with timeout 15 seconds
      then restart
      alert email_address@example.com only on { timeout, nonexist }

Another type of Apache monitoring rule:

	check process apache with pidfile /var/run/apache2.pid
      start "/usr/sbin/service apache2 start"
      stop  "/usr/sbin/service apache2 stop"
      if failed host 127.0.0.1 port 80
        with timeout 15 seconds
      then restart
      if loadavg (1min) greater than 7
        for 5 cycles
      then restart
      alert email_address@example.com only on { timeout, nonexist, resource }

### Ubuntu 14.04: Create a custom Monit Apache status monitoring rule.
	
Check to see if the `apache2.pid` file exists like this:

    ls -la /var/run/apache2/apache2.pid

Now create the actual Apache monitoring rule for Monit:

    sudo nano /etc/monit/conf.d/apache2.conf

One type of Apache monitoring rule:

	check process apache with pidfile /var/run/apache2/apache2.pid
      start "/usr/sbin/service apache2 start"
      stop  "/usr/sbin/service apache2 stop"
      if failed host 127.0.0.1 port 80
        with timeout 15 seconds
      then restart
      alert email_address@example.com only on { timeout, nonexist }

Another type of Apache monitoring rule:

	check process apache with pidfile /var/run/apache2/apache2.pid
      start "/usr/sbin/service apache2 start"
      stop  "/usr/sbin/service apache2 stop"
      if failed host 127.0.0.1 port 80
        with timeout 15 seconds
      then restart
      if loadavg (1min) greater than 7
        for 5 cycles
      then restart
      alert email_address@example.com only on { timeout, nonexist, resource }
	
Restart Monit and all should be good:

    sudo service monit restart

### Create a custom Monit MySQL status monitoring rule.

First, check to see if the `mysqld.pid` file exists:

    ls -la /var/run/mysqld/mysqld.pid

Now create the actual MySQL monitoring rule for Monit:

    sudo nano /etc/monit/conf.d/mysql.conf

One type of MySQL monitoring rule:

	check process mysqld with pidfile /var/run/mysqld/mysqld.pid
	  start program = "/usr/sbin/service mysql start"
	  stop program = "/usr/sbin/service mysql stop"
	  if failed host 127.0.0.1 port 3306 protocol mysql
	    with timeout 15 seconds
	  then restart
	  alert email_address@example.com only on { timeout, nonexist }

Restart Monit and all should be good:

    sudo service monit restart

### Create a custom Monit MongoDB status monitoring rule.

Create the actual MongoDB monitoring rule for Monit:

    sudo nano /etc/monit/conf.d/mongod.conf

One type of MongoDB monitoring rule:

	check process mongod matching "/usr/bin/mongod"
	  start program = "/usr/sbin/service mongod start"
	  stop program = "/usr/sbin/service mongod stop"
	  if failed host 127.0.0.1 port 27017 protocol http
	    with timeout 15 seconds
	  then restart
	  alert email_address@example.com only on { timeout, nonexist }

Restart Monit and all should be good:

    sudo service monit restart

### Create a custom Monit NodeJS application status monitoring rule.

Create the actual MongoDB monitoring rule for Monit:

    sudo nano /etc/monit/conf.d/node_app.conf

One type of NodeJS monitoring rule:

	check process node_app with pidfile "/var/run/node_app.pid"
	  start program = "/sbin/start node_app"
	  stop program  = "/sbin/stop node_app"
	  if failed host 127.0.0.1 port 8080 protocol http
        with timeout 15 seconds
      then restart
      alert email_address@example.com only on { timeout, nonexist }

Restart Monit and all should be good:

    sudo service monit restart

***

*Monit (c) by Jack Szwergold; written on September 15, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*