# Cheat Sheet - Monit

By Jack Szwergold, September 15, 2015

#### Getting `monit` installed and other basics.

First, install `monit` via `aptitude` like this:

    sudo aptitude install monit

Controls `monit` startup; don’t need to adjust for now:

    sudo nano /etc/default/monit

Main config for `monit`; broad adjustments happen here:

    sudo nano /etc/monit/monitrc

Adjust the default daemon values to check services every 60 seconds with a start delay of 120:

	set daemon 60
	with start delay 60

Then find the `mailserver` area of `monitrc` and add the following line. Postfix or an SMTP needs to be active for this to work:

    set mailserver localhost

Create the `conf.d` monit directory if it doesn’t exist:

	sudo mkdir -p /etc/monit/conf.d

Follow the `monit` log to see it in action:

    sudo tail -f -n 200 /var/log/monit.log

If a service becomes “unmonitored” then just remove the `monit` state file like this:

    sudo rm /var/lib/monit/state

Restart `monit` and all should be good:

    sudo service monit restart

#### Create a custom `monit` Apache status monitoring rule.

First, check to see if the `apache2.pid` file exists:

    ls -la /var/run/apache2.pid

Now create the actual Apache monitoring rule for `monit`:

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
	
Restart `monit` and all should be good:

    sudo service monit restart

#### Create a custom `monit` MySQL status monitoring rule.

First, check to see if the `mysqld.pid` file exists:

    ls -la /var/run/mysqld/mysqld.pid

Now create the actual MySQL monitoring rule for `monit`:

    sudo nano /etc/monit/conf.d/mysql.conf

One type of MySQL monitoring rule:

	check process mysqld with pidfile /var/run/mysqld/mysqld.pid
	  start program = "/usr/sbin/service mysql start"
	  stop program = "/usr/sbin/service mysql stop"
	  if failed host 127.0.0.1 port 3306 protocol mysql
	    with timeout 15 seconds
	  then restart
	  alert email_address@example.com only on { timeout, nonexist }

Restart `monit` and all should be good:

    sudo service monit restart

#### Create a custom `monit` MongoDB status monitoring rule.

Create the actual MongoDB monitoring rule for `monit`:

    sudo nano /etc/monit/conf.d/mongod.conf

One type of MongoDB monitoring rule:

	check process mongod matching "/usr/bin/mongod"
	  start program = "/usr/sbin/service mongod start"
	  stop program = "/usr/sbin/service mongod stop"
	  if failed host 127.0.0.1 port 27017 protocol http
	    with timeout 15 seconds
	  then restart
	  alert email_address@example.com only on { timeout, nonexist }

Restart `monit` and all should be good:

    sudo service monit restart

***

*Cheat Sheet - Monit (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*