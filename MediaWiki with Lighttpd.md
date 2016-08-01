# How To Set Up the Latest MediaWiki with Lighttpd on Ubuntu 14.04

By Jack Szwergold

### Introduction

MediaWiki is a popular open source wiki platform that can be used for public or internal collaborative content publishing. MediaWiki is used for many of the most popular wikis on the internet including Wikipedia, the site that the project was originally designed to serve.

In this guide, we will be setting up the latest version of MediaWiki on an Ubuntu 14.04 server. We will use the lighttpd web server to make the actual content available, php-fpm to handle dynamic processing, and mysql to store our wiki's data.

## Prerequisites

To complete this guide, you should have access to a clean Ubuntu 14.04 server instance. On this system, you should have a non-root user configured with sudo privileges for administrative tasks. You can learn how to set this up by following our [Ubuntu 14.04 initial server setup guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04).

When you are ready to continue, log into your server with your sudo user and get started below.

## Install the Server Components

TK

## Step One — Configure MySQL and Create Credentials for MediaWiki

Install MySQL like this:

	sudo apt-get install -y --assume-yes mysql-server

During this initial install it will ask you to setup a root password. Set it to whatever password you wish.

Now secure the MySQL install like this:

	mysql_secure_installation

You will need to know your MySQL `root` password to proceed but basically just respond “Y” to all of the questions except the one that asks to change the root password.

With that done, let’s login to MySQL via the command line like this:

	mysql -u root -p

And create a MediaWiki specific database like this:

	CREATE DATABASE `mediawiki`;

And then create MediaWiki specific MySQL user for that database by running this command; please be sure change the `password` to an actual password you would be using for the install:

    CREATE USER 'mediawiki'@'localhost' IDENTIFIED BY 'password';

Now grant that user access rights to the MediaWiki database by running the following two MySQL commands:

	GRANT USAGE ON `mediawiki`.* TO 'mediawiki'@'localhost' IDENTIFIED BY 'mediawiki';
	GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER ON `mediawiki`.* TO 'mediawiki'@'localhost';

With that done, set the user’s password like this; again please be sure change the `password` to an actual password you would be using for the install:

	SET PASSWORD FOR 'mediawiki'@'localhost' = PASSWORD('password');

And finally, run this command to get MySQL to reload, and recognized the newly adjusted privileges table:

	FLUSH PRIVILEGES;

With that done, let’s move onto getting the MediaWiki stuff itself installed.

## Lighttpd

First, update your repository lists like this:

    sudo apt-get update

And then install Lighttpd like this:

    sudo apt-get install -y --assume-yes lighttpd

Now test the install by going to the URL of your server via a web browser like this:

    http://192.168.56.30

If all went well you’ll see a placeholder page  Lighttpd web page. But if somehow the Lighttpd service does not start, you can force it to come up by running this command:

    sudo service lighttpd start

With that done, let’s move onto getting the PHP stuff installed.

Might need to run this `update-rc.d` command to get the defaults set so Lighttpd comes back up on reboot/cold boot:

    sudo update-rc.d lighttpd defaults

## PHP

Then install PHP modules:

    sudo apt-get install -y --assume-yes php5-cgi php5-mysql

Run this command to ensure the PHP FastCGI modules are enabled:

    sudo lighty-enable-mod fastcgi fastcgi-php

With that done, run this command to enable the changes in Lighttpd:

    sudo service lighttpd force-reload

Now, test PHP by creating a simple test file like this:

    sudo nano /var/www/test.php

And the contents of that file should be something like this:

	<?php

	phpinfo();

	?>

And now test that file by loading it in your web browser like this:

    http://192.168.56.30/test.php

If everything has worked out well, the page should load with the standard PHP info page. And to make sure that CGI/FastCGI is enabled, check under the “Server API.”

With that done, let’s move onto getting the MySQL stuff installed.

## MediaWiki

First, let’s prep the web root of the server by removing unnecessary test files that were placed during setup by running this `rm` command:

    sudo rm /var/www/index.lighttpd.html /var/www/test.php

    curl -O https://releases.wikimedia.org/mediawiki/1.27/mediawiki-1.27.0.tar.gz

    tar xvzf mediawiki-*.tar.gz

    sudo mv mediawiki-*/ /var/www/w

    sudo chown -R www-data:www-data /var/www/w

Now head over to your server’s IP address to the root path like this:

    http://192.168.56.30/w

And if all has gone well, you should see a page that says:

> LocalSettings.php not found.

> Please set up the wiki first.

So just click that “set up the wiki” link and let’s begin the setup process.

First, select the desired languages and hit continue.

Next up will be a page that does basic system environment checks. If all went well, you should see the following text in green near the middle of the page:

> The environment has been checked. You can install MediaWiki.

If that’s all good, just click “Continue” to move onto the next step which should be setting up MySQL credentials that we have setup previously.

* The database host should be: `localhost`
* The database name should be: `mediawiki`
* The database table prefix should be blank.
* The database username and password should be set to what you set them to previously.

When that is set, hit “Continue” and move onto the next database settings page. The “Storage Engine” and “Database Character Set” values should be fine so just hit “Continue” and move onto the Wiki specific details.

Here you would choose a name of the Wiki as well as set basic wiki administrator specific account info; just set it up whichever way you please.

Click continue and you can set and adjust other wiki specific options, but you can just click “Continue” and accept the defaults; everything can be changed later on anyway.

The next, penultimate page will state:

> By pressing "Continue →", you will begin the installation of MediaWiki. If you still want to make changes, press "← Back".

If all proceeds as expected, you will reach the final “Complete!” screen and a file named `LocalSettings.php` will be downloaded via your browser. You need to get that file from your local machine in some way and install it right in your MediaWiki install root.

For example, you could copy it to your user’s home directory with an SCP command like this:

    scp /Users/jack/Downloads/LocalSettings.php [usermame]@[hostname]:.

And just copy it over to the MediaWiki root by running this command:

    sudo mv ~/LocalSettings.php /var/www/w/LocalSettings.php

And once that is done, you can just head over to your server’s main URL via your web browser and your MediaWiki setup should be good to go:

    http://192.168.56.30/w/

***

*MediaWiki with Lighttpd (c) by Jack Szwergold; written on August 1, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*