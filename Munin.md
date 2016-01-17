# Munin

By Jack Szwergold, September 16, 2015

***

#### Install `munin`.

Install Munin on Ubuntu 12.04:

	sudo aptitude install munin munin-node
	
Install Munin on Ubuntu 14.04:

	sudo aptitude install munin munin-node munin-plugins-extra libwww-perl
	
#### Basic `munin` usage items.

Control the Munin node:

	sudo service munin-node start
	sudo service munin-node stop
	sudo service munin-node restart

Follow the Munin logs:

	sudo tail -f -n 200 /var/log/munin/munin-graph.log
	sudo tail -f -n 200 /var/log/munin/munin-html.log
	sudo tail -f -n 200 /var/log/munin/munin-limits.log
	sudo tail -f -n 200 /var/log/munin/munin-node.log
	sudo tail -f -n 200 /var/log/munin/munin-update.log

#### For Ubuntu 12.04, do some initial tweaks to get things running smoothly.

First open up the `munin-node` config file:

	sudo nano /etc/munin/plugin-conf.d/munin-node

MySQL InnoDB warnings in Munin are a bit nuts. Unless you really need data from MySQL InnoDB status, just disable MySQL InnoDB warnings:

	[mysql_innodb]
	env.warning 0
	env.critical 0

Or if that does not work, just disable the plugin by removing the symbolic link like this:

	sudo rm /etc/munin/plugins/mysql_innodb
	
Get IOstat stuff to properly report data from drives that have a number in their name; oddly the plugin by default ignores drives that have numbers in their name. Why? I don’t know. Makes no sense if you ask me. Just add this config line and all should be good:

	[iostat]
	env.SHOW_NUMBERED 1

Also, sometimes the basic networking error reporting acts wonky. So just disable the `if_err_eth0` and `if_err_eth1` plugins by removing the symbolic link like this:

	sudo rm /etc/munin/plugins/if_err_eth0
	sudo rm /etc/munin/plugins/if_err_eth1

Now just restart the Munin node to get the settings loaded and you should be good:

	sudo service munin-node restart

#### Sundry `munin` plug-in stuff.

Check status of available Munin plug-ins:

    sudo /usr/sbin/munin-node-configure

Suggest available Munin plug-ins:

    sudo /usr/sbin/munin-node-configure --suggest

Activate the Apache related Munin plug-ins.

	sudo ln -s /usr/share/munin/plugins/apache_accesses /etc/munin/plugins/apache_accesses
	sudo ln -s /usr/share/munin/plugins/apache_processes /etc/munin/plugins/apache_processes
	sudo ln -s /usr/share/munin/plugins/apache_volume /etc/munin/plugins/apache_volume

Activate the MySQL related Munin plug-ins.

	sudo ln -s /usr/share/munin/plugins/mysql_bytes /etc/munin/plugins/mysql_bytes
	sudo ln -s /usr/share/munin/plugins/mysql_queries /etc/munin/plugins/mysql_queries
	sudo ln -s /usr/share/munin/plugins/mysql_slowqueries /etc/munin/plugins/mysql_slowqueries
	sudo ln -s /usr/share/munin/plugins/mysql_threads /etc/munin/plugins/mysql_threads

Activate the Postfix related Munin plug-ins.

	sudo ln -s /usr/share/munin/plugins/postfix_mailqueue /etc/munin/plugins/postfix_mailqueue
	sudo ln -s /usr/share/munin/plugins/postfix_mailvolume /etc/munin/plugins/postfix_mailvolume

Activate the Fail2Ban related Munin plug-ins.

    sudo ln -s /usr/share/munin/plugins/fail2ban /etc/munin/plugins/fail2ban

If you are using the Fail2Ban plug-in, open up the `munin-node` config file:

	sudo nano /etc/munin/plugin-conf.d/munin-node

And add this simple config item at the bottom of the file:

    [fail2ban]
    user root

#### Setting up Munin email alerts.

Munin can send out email alerts to let you know if anything it’s monitoring is not working as expected. Follow these steps to get the emailing functionality up and running.

First, open up the `munin.conf` file:

	sudo nano /etc/munin/munin.conf

Now look for a chunk of configuration that looks like this; note all the examples use `contact.` format:

	# Drop somejuser@fnord.comm and anotheruser@blibb.comm an email everytime
	# something changes (OK -> WARNING, CRITICAL -> OK, etc)
	#contact.someuser.command mail -s "Munin notification" somejuser@fnord.comm
	#contact.anotheruser.command mail -s "Munin notification" anotheruser@blibb.comm
	#

Enter a line at the bottom of that text to setup the `contact.serveralert` like this:

	contact.serveralert.command mail -s "MUNIN - ${var:group} :: ${var:host}" email_address@example.com
	contact.serveralert.always_send warning critical

Now find the “host tree” to add `contacts serveralert` at the bottom of that host’s config:

	# a simple host tree
	[sandbox.local]
	    address 127.0.0.1
	    use_node_name yes
	    contacts serveralert

#### Adjusting the `unknown_limit` to prevent excessive warnings from popping up and being emailed.

When a device or process doesn’t return data to the Munin node, Munin just interprets that as “unknown.” Lots of times this is just a minor blip and not an outage or even a concern.

So it’s best to just raise the `unknown_limit` to make sure you’re not deluged with a flood of “The sky is falling!” emails and alerts.

First, open up the `munin.conf` file:

	sudo nano /etc/munin/munin.conf

And add this to the bottom of the file:

	# 2014-07-14: Adding an 'unknown' limit to prevent excessive warnings from being sent out.
	unknown_limit 18

Now just restart the Munin node to get the settings loaded and you should be good:

	sudo service munin-node restart

But that said, in some cases, setting the `unknown_limit` in `munin.conf` doesn’t really change anything. So in that case, it’s just best to “hack” the core Munin script to adjust that `unknown_limit` value.

So, open up the `LimitsOld.pm` file:

    sudo nano /usr/share/perl5/Munin/Master/LimitsOld.pm

Search for the line that reads something like this:

    my $unknown_limit = munin_get($hash, "unknown_limit", 3);

And adjust the value in there from `3` to `30` like this:

    my $unknown_limit = munin_get($hash, "unknown_limit", 30);

Now just restart the Munin node to get the settings loaded and you should be good:

	sudo service munin-node restart

#### Setting an Apache config for `munin` in Ubuntu 12.04.

Munin will set a symbolic link to it’s own Apache config, but it’s not that hot so let’s get rid of it:

	sudo rm /etc/apache2/conf.d/munin
	
Now let’s create our own `munin.conf` like this:

	sudo nano /etc/apache2/conf.d/munin.conf
	
Here is an example of a basic, non-secure Apache config for `munin`:

	<Directory "/var/cache/munin/www">
	  Options Indexes MultiViews FollowSymLinks
	  AllowOverride None
	
	  <IfModule mod_expires.c>
	    ExpiresActive On
	    ExpiresDefault M310
	  </IfModule>
	
	</Directory>

Here is an example of a basic, secure Apache config for `munin`. Note the `Allow from` exceptions; feel free to add any IP address you wish to bypass that secure setup to that list:

	<Directory "/var/cache/munin/www">
	  Options Indexes MultiViews FollowSymLinks
	  AllowOverride None
	
	  AuthName "Munin Access"
	  AuthType Basic
	  require valid-user
	  AuthUserFile /etc/apache2/htpasswd_munin
	
	  Order Deny,Allow
	  Deny from all
	  Allow from 127.0.0.1 ::1
	  Allow from localhost
	  Allow from 192.168
	  Allow from 10
	  Satisfy Any
	
	  <IfModule mod_expires.c>
	    ExpiresActive On
	    ExpiresDefault M310
	  </IfModule>
	
	</Directory>

#### Setting an Apache config for `munin` in Ubuntu 14.04.

Munin will set a symbolic link to it’s own Apache config, but it’s not that hot so let’s get rid of it:

	sudo rm /etc/apache2/conf-available/munin.conf
	sudo rm /etc/apache2/conf-enabled/munin.conf

Now let’s create our own `munin.conf` like this:

	sudo nano /etc/apache2/conf-available/munin.conf

Here is an example of a basic, non-secure Apache config for `munin`:

	<Directory "/var/cache/munin/www">
	  Require all granted
	  Options FollowSymLinks SymLinksIfOwnerMatch
	
	  <IfModule mod_expires.c>
	    ExpiresActive On
	    ExpiresDefault M310
	  </IfModule>

	</Directory>

Here is an example of a basic, secure Apache config for `munin`. Note the `Allow from` exceptions; feel free to add any IP address you wish to bypass that secure setup to that list:

	<Directory "/var/cache/munin/www">
	  Options Indexes MultiViews FollowSymLinks
	  AllowOverride None
	
	  AuthName "Munin Access"
	  AuthType Basic
	  require valid-user
	  AuthUserFile /etc/apache2/htpasswd_munin
	
	  Require all denied
	  Require ip 127.0.0.1 ::1
	  Require host localhost
	  Require ip 192.168
	  Require ip 10
	
	  <IfModule mod_expires.c>
	    ExpiresActive On
	    ExpiresDefault M310
	  </IfModule>
	
	</Directory>

With that done, be sure to enable the AWStats Apache module like this:

   sudo a2enconf munin

***

And if you are using the secure setup, be sure to setup the `/etc/apache2/htpasswd_munin` that config refers to like this:

    sudo htpasswd -c /etc/apache2/htpasswd_munin munin

That command will initiate the process to create an `htpasswd` for a user named `munin` in the file named `htpasswd_munin`. When prompted, enter whatever password you would like to use.

***

*Munin (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*