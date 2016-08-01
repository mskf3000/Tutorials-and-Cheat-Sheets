# How To Set Up the Latest MediaWiki with Lighttpd on Ubuntu 14.04

By Jack Szwergold

### Introduction

MediaWiki is a popular open source wiki platform that can be used for public or internal collaborative content publishing. MediaWiki is used for many of the most popular wikis on the internet including Wikipedia, the site that the project was originally designed to serve.

In this guide, we will be setting up the latest version of MediaWiki on an Ubuntu 14.04 server. We will use the lighttpd web server to make the actual content available, php-fpm to handle dynamic processing, and mysql to store our wiki's data.

## Prerequisites

To complete this guide, you should have access to a clean Ubuntu 14.04 server instance. On this system, you should have a non-root user configured with sudo privileges for administrative tasks. You can learn how to set this up by following our [Ubuntu 14.04 initial server setup guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04).

When you are ready to continue, log into your server with your sudo user and get started below.

## Step One — Install the Server Components

First, update your repository lists like this:

    sudo apt-get update

TK

## Step Two — Configure MySQL and Create Credentials for MediaWiki

Now that we know the server is properly setup, let’s get MySQL installed like this:

	sudo apt-get install -y --assume-yes mysql-server

At some point during initial MySQL install, the process will prompt you to choose a **root** password. Type in whatever **root** password you feel comfortable with and hit return to move on.

Now, let’s do some basic MySQL hardening by running this command:

	mysql_secure_installation

You’ll be prompted for the MySQL **root** password that was just created; enter that and hit return. Answer the remaining prompts as follows:

* **Change the root password?** Select **N** for “No.”
* **Remove anonymous users?** Select **Y** for “Yes.”
* **Disallow root login remotely?** Select **Y** for “Yes.”
* **Remove test database and access to it?** Select **Y** for “Yes.”
* **Reload privilege tables now?** Select **Y** for “Yes.”

With that all done, let’s login into your server’s newly installed MySQL instance with this command; note you will be prompted for your **root** password:

	mysql -u root -p

And once you’re at the MySQL command prompt, let’s create a MediaWiki specific database—named **mediawiki**—like this:

	CREATE DATABASE `mediawiki`;

With that done, let’s now create a MediaWiki specific user—with exclusive rights to that **mediawiki** database—by running this command; please be sure change the **[PASSWORD]** text in this example to the actual password you would be using for the install:

    CREATE USER 'mediawiki'@'localhost' IDENTIFIED BY '[PASSWORD]';

Now grant that **mediawiki** user access rights to the **mediawiki** database by running the following two MySQL **GRANT** commands:

	GRANT USAGE ON `mediawiki`.* TO 'mediawiki'@'localhost' IDENTIFIED BY 'mediawiki';
	GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER ON `mediawiki`.* TO 'mediawiki'@'localhost';

As a final step, we need to set the user’s password again like this; please be sure change the **[PASSWORD]** text in this example to the actual password you would be using for the install:

	SET PASSWORD FOR 'mediawiki'@'localhost' = PASSWORD('[PASSWORD]');

And with that done, we need to run this command to get MySQL to reload—and recognize—the newly adjusted grant table settings:

	FLUSH PRIVILEGES;

Once that’s done you can exit MySQL by simply typing `exit;` and hitting return which will get you back to your server’s terminal shell where we can move onto the next step: Getting Lighttpd installed and configured.

## Configure Lighttpd and PHP-FPM

We are going to be using Lighttpd and PHP-FPM as our web server for MediaWiki in this tutorial, so let’s go ahead and install it like this.

First, run this command to install Lighttpd itself:

    sudo apt-get install -y --assume-yes lighttpd

With that done you can test the install to see if Lighttpd is up and runnig by opening up a web browser and pointing it to the domain name or IP of your server like this; change `domain_name_or_IP` to match the domain name or IP address of the server you are working on:

    http://domain_name_or_IP

If all went well you’ll see a defauly placeholder Lighttpd web page. But if somehow the Lighttpd service does not start, you can force it to come up by running this command:

    sudo service lighttpd start

***

**Note:** While Lighttpd should normally be set as a startup service during the install process, one might need to run this `update-rc.d` command to get the defaults set so Lighttpd comes back up on reboot/cold boot:

    sudo update-rc.d lighttpd defaults

***

Now that Lighttpd is setup and runnning, let’s install the PHP related items like this:

    sudo apt-get install -y --assume-yes php5-cgi php5-mysql

And once those are installed, run this command to ensure the PHP FastCGI modules are enabled:

    sudo lighty-enable-mod fastcgi fastcgi-php

With that done, let’s restart Lighttpd to get those modules loaded:

    sudo service lighttpd restart

And now, let’s test the PHP install by creating a simple test file like this; I’m using **nano** for a text editor in this example, but feel free to use whatever text editor you prefer:

    sudo nano /var/www/test.php

And the contents of that file should be something like this:

	<?php

	phpinfo();

	?>

Save that newly created *test.php*, exit your text editor, and now load that file it in your web browser like this:

    http://domain_name_or_IP/test.php

If everything goes as planned, that page should load with the standard PHP info page that reflects all current PHP—and related—server settings on that server. And to make sure that everything is working as we want, check under the **Server API** and make sure it’s set to **CGI/FastCGI**.

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