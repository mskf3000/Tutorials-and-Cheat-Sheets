## Ubuntu - Basic System Setup

By Jack Szwergold, January 16, 2016

### Set up the base system.

#### Basic user and group setup.

Create the default user; choose your own name instead of this default of `[default]`:

	sudo adduser [default]
	
Edit the `sudoers` file.

    sudo nano /etc/sudoers

Add this entry under: User privilege specification; use the real name instead of the default of `[default]`:

    [default] ALL=(ALL:ALL) ALL

Now secure the `root` account by locking it:

    sudo passwd -l root

#### Basic user and group setup.

Create the `www-readwrite` group:

    sudo groupadd www-readwrite

Set the `[default]` user’s main group to `www-readwrite`:

    sudo usermod -g www-readwrite [default]

Add user to the `www-readwrite` group:

    sudo adduser [default] www-readwrite

#### Server zone and timezone stuff.

Set the server time and timezone info:

	sudo ntpdate -u ntp.ubuntu.com
	
	sudo dpkg-reconfigure tzdata

Set the NTP client to check the server daily:

    sudo nano /etc/cron.daily/ntpdate

Add this to that file:

    ntpdate -s -u ntp.ubuntu.com

Make sure the file has executable permissions:

    sudo chmod 755 /etc/cron.daily/ntpdate

#### Install `aptitude` and adjust the sources list.

Update `apt-get`:

	sudo apt-get update

Install `aptitude`:

	sudo apt-get install aptitude

Edit the `sources.list` to enable partner package updates.

    sudo nano /etc/apt/sources.list

Uncomment the following lines to add Canonical’s `partner` repository:

	deb http://archive.canonical.com/ubuntu/ precise partner
	deb-src http://archive.canonical.com/ubuntu/ precise partner

	sudo aptitude update
	
	sudo aptitude upgrade

#### Install and configure `sysstat`.

Install and enable `sysstat`:

    sudo aptitude install sysstat

Edit `sysstat`:

    sudo nano /etc/default/sysstat

Enable `sysstat`:

    ENABLED="true"

Restart `sysstat`:

	sudo service sysstat restart

#### Install base level tools.

Run this command to install more base level tools:

	sudo aptitude install \
	  dnsutils traceroute nmap bc htop finger curl whois rsync lsof \
	  iftop figlet lynx mtr-tiny iperf nload zip unzip attr sshpass \
	  dkms mc elinks ntp dos2unix p7zip-full nfs-common imagemagick \
	  slurm sharutils uuid-runtime chkconfig quota pv trickle apachetop

#### Install the VirtualBox Guest DKMS Stuff.

Only necessary if you are running a VirtualBox install.

    sudo aptitude install virtualbox-guest-dkms

#### Install `locate`.

Install `locate`:

	sudo aptitude install locate

When that is done, update the `locate` database like this:

	sudo updatedb

#### Adjust the `locale` settings.

Run `update-locale`:

    sudo update-locale

Might need to set the locale file to this:

	sudo nano /etc/default/locale
	
	LANG="en_US.UTF-8"

#### Install development tools and version control items.

Install the `build-essential` tools to allow compiling source code.

    sudo aptitude install build-essential

Install Git and Subversion related stuff:

	sudo aptitude install git git-core subversion git-svn

#### Install Postfix and other mail utilities.

Install Postfix and related mail utilities:

    sudo aptitude install postfix mailutils

Check the Postfix config:

    sudo nano /etc/postfix/main.cf

Change these to match your server settings:

	myhostname = preworn.com
	mydestination = preworn.com, localhost.localdomain, localhost

#### Adjust MOTD stuff.

Create a header with Figlet like this:

	figlet Preworn
	
For Ubuntu 12.04, add that to the MOTD file like this:

	sudo nano /etc/motd.tail

For Ubuntu 14.04, add that to the MOTD file like this:

	sudo nano /etc/motd

#### Adjusting the default UMASK values.

The goal of this stuff is to allow default group writing for all users. Adjusting `002` to `022` would set directory permissions to `775` and file permissions to `664`.

Adjust the values in `login.defs`:

	sudo nano /etc/login.defs
	
	# UMASK         022
	UMASK           002

Adjust the values in the `common-session` file connected to `pam.d`:

	sudo nano /etc/pam.d/common-session

Find the line that reads like this:

	session optional                        pam_umask.so

And change it to this; note the `umask=0002` at the end:

	session optional                        pam_umask.so	umask=0002

#### Fix for slow SSH client connections.

Fix for slow SSH client connections by changing the preferred order of authentications for all users on a system-wide level:

	sudo nano /etc/ssh/ssh_config
	
	PreferredAuthentications publickey,password,gssapi-with-mic,hostbased,keyboard-interactive

#### Install IPTables and IPSet.

Install the basic IPTables and IPSet stuff:

	sudo aptitude install iptables iptables-persistent ipset

Import the default IPSet rules:

	sudo ipset restore < ipset.conf

Set copy the IPSet rules to the location used by `iptables-persistent`:
	
	sudo cp ~/ipset.conf /etc/iptables/rules.ipsets

Import the default IPTables rules:

	sudo iptables-restore < iptables.conf
	
Set copy the IPTables rules to the location used by `iptables-persistent`:

	sudo cp ~/iptables.conf /etc/iptables/rules.v4

***

## Install Apache and PHP.

#### Install the core Apache stuff.

For Ubuntu 12.04, install the core Apache stuff with this command:

	sudo aptitude install \
	  apache2 apache2-threaded-dev php5 \
	  libapache2-mod-php5 php-pear

For Ubuntu 14.04, install the core Apache stuff with this command:

	sudo aptitude install \
	  apache2 apache2-dev apache2-utils php5 \
     libapache2-mod-php5 php-pear

Now install the PHP5 modules:

	sudo aptitude install \
	  php5-mysql php5-pgsql php5-odbc php5-sybase php5-sqlite \
	  php5-xmlrpc php5-json php5-xsl php5-curl php5-geoip \
	  php-getid3 php5-imap php5-ldap php5-mcrypt \
	  php5-pspell php5-gmp php5-gd

And for some reason, the MCrypt module needs to be enabled like this:

    sudo php5enmod mcrypt

#### Enable a few core Apache modules.

Enable these basic Apache modules:

	sudo a2enmod rewrite headers expires include proxy proxy_http cgi

#### Harden PHP.

Open up the main PHP config file like this:

	sudo nano /etc/php5/apache2/php.ini

Locate `expose_php` and disable:

	expose_php = Off

#### Harden Apache.

In Ubuntu 12.04, open up the Apache this `security` config to adjust it:

    sudo nano /etc/apache2/conf.d/security

In Ubuntu 14.04, open up the Apache this `security` config to adjust it:

    sudo nano /etc/apache2/conf-available/security.conf

Locate `ServerTokens` and set to production:

    ServerTokens Prod

Locate `ServerSignature` and disable:

    ServerSignature Off

Locate `TraceEnable` and disable:

	TraceEnable Off

#### Set a simpler default Apache configuration.

For Ubuntu 12.04, open up the `default` config for adjustments:

    sudo nano /etc/apache2/sites-available/default

For Ubuntu 14.04, open up the `000-default.conf` config for adjustments:

    sudo nano /etc/apache2/sites-available/000-default.conf

And whichever one is opened, just replace the contents with this more simplified version of the config:

	<VirtualHost *:80>
	  DocumentRoot /var/www
	
	  CustomLog ${APACHE_LOG_DIR}/access.log combined
	  ErrorLog ${APACHE_LOG_DIR}/error.log
	
	  # Possible values include: debug, info, notice, warn, error, crit, alert, emerg.
	  LogLevel warn
	
	  # AWStats aliases.
	  Alias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	  Alias /awstatsclasses /usr/share/awstats-7.3/wwwroot/classes/
	  Alias /awstatscss /usr/share/awstats-7.3/wwwroot/css/
	  Alias /awstatsicons /usr/share/awstats-7.3/wwwroot/icon/
	  Alias /icon /usr/share/awstats-7.3/wwwroot/icon/
	
	  # Other aliases.
	  Alias /munin /var/cache/munin/www
	  Alias /phpmyadmin /usr/share/phpmyadmin
	
	  RedirectMatch 404 /(builds|configs|content)(/|$)
	
	  # 2013-11-04: Including common items in a common file for ssl & non-ssl.
	  include /etc/apache2/sites-available/common.conf
	
	  # 2014-03-12: Including common ModSecurity related items.
	  # include /etc/apache2/sites-available/common_mod_security.conf
	
	</VirtualHost>

#### Set the `common.conf` configuration file.

This “common” configuration is designed to contain some basic commands not related to authorization and access on an Apache server. I’ve set this stuff in a separate file to make it easier to enable/disable new items in this vein across a few different servers.

Set the `common.conf` Apache config.

	sudo nano /etc/apache2/sites-available/common.conf

For Ubuntu 12.04, open up use this version of the `common.conf` file:

	# 2013-03-19: Added to block hidden version control directories.
	RewriteEngine On
	RewriteRule ^(.*/)?\.git+ - [F,L]
	
	# 2014-04-11: Added to block Ruby related items.
	<Files ~ "\.(rb|ru|yml|yaml)$">
	  Order allow,deny
	  Deny from all
	</Files>

For Ubuntu 14.04, open up use this version of the `common.conf` file:

    # 2013-03-19: Added to block hidden version control directories.
    RewriteEngine On
    RewriteRule ^(.*/)?\.git+ - [F,L]

    # 2014-04-11: Added to block Ruby related items.
    <Files ~ "\.(rb|ru|yml|yaml)$">
      Require all denied
    </Files>

#### Set a simple Apache `index.php` file.

Set a nicer default index instead of that silly “It works!” HTML file Apache comes with.

For Ubuntu 12.04, remove the `index.html` file:

    sudo rm /var/www/index.html

For Ubuntu 14.04, remove the `html` directory:

    sudo rm -rf /var/www/html

Then create the `index.php` file:

    sudo nano /var/www/index.php

And here are the contents of the `index.php` file:

	<?php
	
	# phpinfo();
	# header('Location: http://www.preworn.com/');
	echo $_SERVER['SERVER_NAME'];
	# echo '<br />';
	# echo $_SERVER['SERVER_ADDR'];
	
	?>

#### Adjust Apache config to allow group `www-readwrite` access.

Open up the Apache `envvars` file:

    sudo nano /etc/apache2/envvars

And the change the `APACHE_RUN_GROUP` to `www-readwrite`:

	# export APACHE_RUN_GROUP=www-data
	export APACHE_RUN_GROUP=www-readwrite

Then this to the end of the file to adjust the UMASK value to `002`:

	umask 002

#### Adjust Apache logs to allow group `www-readwrite` access.

	sudo chmod o+rx /var/log/apache2
	
	ls -latr /var/log/apache2/
	
	ls -la /var/log/apache2/*.log
	
	sudo chgrp www-readwrite /var/log/apache2/*
	
	sudo chmod 644 /var/log/apache2/*

Open up the Apache LogRotate script like this:
	
	sudo nano /etc/logrotate.d/apache2

Replace it’s contents with this stuff:

	/var/log/apache2/*.log {
	        weekly
	        missingok
	        rotate 13
	        compress
	        delaycompress
	        notifempty
	        create 640 root www-readwrite
	        sharedscripts
	        postrotate
	                /etc/init.d/apache2 reload > /dev/null
	        endscript
	        prerotate
	                if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
	                        run-parts /etc/logrotate.d/httpd-prerotate; \
	                fi; \
	        endscript
	}

***

## Install MySQL.

Install MySQL like this:

    sudo aptitude install mysql-server mysql-client

Secure the install:

    sudo mysql_secure_installation

To prevent MySQL from coming up on reboot—for setups that don’t always use a database but it would be nice to have—run this command to stop MySQL:

    sudo service mysql stop

Then remove the startup script like this:

    sudo update-rc.d -f mysql remove

***

# Old stuff that’s out of date and possibly not needed.

Need to review this stuff. Here for reference only.

An older way of setting default UMASK in Ubuntu & other Debian setups

	sudo nano /etc/profile

Add this to the last line:

	# umask 022
	umask 002

#### Install SASS.

	sudo aptitude install ruby-full rubygems
	
	sudo gem install sass
	
	sudo ln -s /var/lib/gems/1.8/bin/sass /usr/local/bin/sass

#### Install LESS.

	sudo gem install therubyracer less
	
	sudo ln -s /var/lib/gems/1.8/bin/lessc /usr/local/bin/lessc

#### Install SVN2GIT.

	sudo aptitude install ruby rubygems
	
	sudo gem install svn2git
	
	sudo ln -s /var/lib/gems/1.8/bin/svn2git /usr/local/bin/svn2git

#### For PHP/PEAR stuff MSSQL connections.

	sudo aptitude install php-mdb2 php-mdb2-driver-mysql php-mdb2-driver-pgsql php-mdb2-driver-sqlite
	
	sudo pear install --nodeps MDB2_Driver_mssql
	
	sudo pear install HTTP_Request

***

*Ubuntu - Basic System Setup (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*