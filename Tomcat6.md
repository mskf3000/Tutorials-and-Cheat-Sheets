## Tomcat6

By Jack Szwergold

### Install Tomcat6.

Install Tomcat6 via `aptitude` like this:

    sudo aptitude install tomcat6

Once that is installed open up the `tomcat6` config file:

    sudo nano /etc/default/tomcat6

Find the line for `JAVA_HOME` and change as follows:

	#JAVA_HOME=/usr/lib/jvm/openjdk-6-jdk
	JAVA_HOME=/usr/lib/jvm/java-6-oracle/jre

And then find the line for `JAVA_OPTS` and change as follows:

	# JAVA_OPTS="-Djava.awt.headless=true -Xmx128m -XX:+UseConcMarkSweepGC"
	JAVA_OPTS="-Xms128m -Xmx256m -XX:MaxPermSize=512m -Djava.awt.headless=true -XX:-UseGCOverheadLimit -XX:+UseCompressedOops"

#### Sundry Tomcat6 items on an Ubuntu/Debian system.

Get Tomcat6 version:

	/usr/share/tomcat6/bin/catalina.sh version

Output should be something like this:

	Using CATALINA_BASE:   /usr/share/tomcat6
	Using CATALINA_HOME:   /usr/share/tomcat6
	Using CATALINA_TMPDIR: /usr/share/tomcat6/temp
	Using JRE_HOME:        /usr/lib/jvm/java-6-oracle/jre
	Using CLASSPATH:       /usr/share/tomcat6/bin/bootstrap.jar
	Server version: Apache Tomcat/6.0.35
	Server built:   Jun 22 2015 01:28:45
	Server number:  6.0.35.0
	OS Name:        Linux
	OS Version:     3.13.0-63-generic
	Architecture:   amd64
	JVM Version:    1.6.0_45-b06
	JVM Vendor:     Sun Microsystems Inc.

Start, stop and control Tomcat6 on an Ubuntu/Debian system:

	sudo service tomcat6 start
	sudo service tomcat6 stop
	sudo service tomcat6 restart
	sudo service tomcat6 try-restart
	sudo service tomcat6 force-reload
	sudo service tomcat6 status

#### Fix a myriad of Tomcat6 directory ownership and permissions stuff.

First, set the group ownership of `/var/lib/tomcat6/` to `www-readwrite`:

	sudo chown tomcat6:www-readwrite -R /var/lib/tomcat6/

And now grant group write privileges to `/var/lib/tomcat6/`:

	sudo chmod g+w -R /var/lib/tomcat6/

Now, note how group write permissions are not set in `/etc/tomcat6`:

    ls -la /etc/tomcat6

Fix that like this:

    sudo chmod g+w -R /etc/tomcat6

Then check in the `/usr/share/tomcat6/`:

    ls -la /usr/share/tomcat6

We need to setup the `server/classes/` directory like this:

    sudo mkdir -p /usr/share/tomcat6/server/classes

Then change ownership of the directory like this:

    sudo chown -R tomcat6:www-readwrite -R /usr/share/tomcat6

#### Adjust Tomcat6 `logrotate.d` directory permissions.

Change the Tomcat6 logs directory (`/var/log/tomcat6`) so the owner is `root` and the group is `tomcat6`:

	sudo chown root:tomcat6 /var/log/tomcat6

Now change the files in that `/var/log/tomcat6` directory to have `tomcat6` as the owner and `root` as the group:
	
	sudo chown tomcat6:root /var/log/tomcat6/*

Adjust the permissions to the `/var/log/tomcat6` directory so group members can execute, read and write and others can read:

	sudo chmod 775 /var/log/tomcat6
	
Adjust the permissions to the files in the `/var/log/tomcat6` directory so group members can read and write and others can read:

	sudo chmod 664 /var/log/tomcat6/*

#### Adjust Tomcat6 the `logrotate` config file.

The default Tomcat6 `logrotate` config needs to be tweaked as well. So open up the `tomcat6` `logrotate.d` config file:

    sudo nano /etc/logrotate.d/tomcat6

It should look something like this:

	/var/log/tomcat6/catalina.out {
	  copytruncate
	  weekly
	  rotate 52
	  compress
	  missingok
	  create 640 tomcat6 adm
	}

Change that so the `delaycompress` is added to the options, the new owner is `tomcat6`, the group is `root` and file permissions are `664`:

	/var/log/tomcat6/catalina.out {
	  copytruncate
	  weekly
	  rotate 52
	  compress
	  delaycompress
	  missingok
	  create 664 tomcat6 root
	}

#### Adjusting Tomcat6 group ownership permissions.

**NONE OF THIS WORKS! But placing it here for reference anyway.**

This method doesn’t work.

    sudo nano /etc/default/tomcat6

	#TOMCAT6_GROUP=tomcat6
	TOMCAT6_GROUP=www-readwrite

And this other method doesn’t work.

    sudo nano /etc/init.d/tomcat6

	#TOMCAT6_GROUP=tomcat6
	TOMCAT6_GROUP=www-readwrite

Set the `tomcat6` user’s default group to the `www-readwrite` group:

	sudo usermod -g www-readwrite tomcat6

Add the `tomcat6` user to the `www-readwrite` group:

    sudo adduser tomcat6 www-readwrite

Change permissions on the following directories:

	sudo chown tomcat6:www-readwrite -R /etc/tomcat6
	sudo chown tomcat6:www-readwrite -R /usr/share/tomcat6
	sudo chown tomcat6:www-readwrite -R /var/log/tomcat6

***

*Tomcat6 (c) by Jack Szwergold; written September 24, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*