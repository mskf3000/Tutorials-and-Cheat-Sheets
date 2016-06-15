## Developing With MAMP 2.1.4

By Jack Szwergold

### Part 1: Setting Up a Basic, Useful MAMP Environment

I like using MAMP because it’s a quick and easy way to get a very basic—and very standard—LAMP (Linux, Apache, MySQL and PHP) environment working on a Mac. While there are some purists who believe that coding on a pure Linux setup—virtual or physical—is the way the go, for pretty much every PHP task I have ever come across, MAMP has always worked well. And since it operates on on the Mac desktop like any other application, it allows me to use the great development tools that exist for Macs without much hassle. A complete win-win development setup for Mac developers who code in the LAMP world.

That said, there are some simple tweaks I like to do on any MAMP install that will make my life as a developer easier. If you are familiar with basic PHP ini configuration changes, you’ll pick up what I am doing right away.

#### Let’s get started.

Before anything else, you need to figure out which version of the PHP ini configuration file—commonly referred to as `php.ini`—your setup is using. For this example, I am using PHP version 5.4.10 which is part of the standard MAMP 2.1.4 install. I am also using the MAMP specific non-default port of `8888`.

While port `80` is the traditionally used port for web services, I find it’s better to use port `8888` since you do not have to enter an administrator password each time you launch MAMP. Also, it’s a good habit to program web applications in a way that that allows you to easily migrate a codebase from server to server. Using port `8888` is so non-standard it forces you to code with flexibility in mind which in the end can only help your coding skills.

You can determine which version of PHP is being used by looking through the output of [phpinfo()][1] for the value called `Loaded Configuration File` near the top of the page. You can easily view the contents of `phpinfo()` in MAMP by choosing *phpInfo* item from the top menu of the main MAMP page:

	http://localhost:8888/MAMP/

Or you can just get to it directly via this URL:

	http://localhost:8888/MAMP/phpinfo.php

Once you find the `Loaded Configuration File` value and you figure out which PHP ini configuration file you are using, you can then edit the config file like so. I like to use [BBEdit][2], so this is how I call it from the command line:

	bbedit /Applications/MAMP/bin/php/php5.4.10/conf/php.ini

That will open BBEdit via the command line and open the `php.ini` at the same time. Or if you are more comfortable doing it all in the command line, you can open the file with [GNU nano][3]—the command line editor I like to use which comes with all Mac OS X installs—like so:

	nano /Applications/MAMP/bin/php/php5.4.10/conf/php.ini

Whatever editor you choose to use is not important. So don’t feel tied to my examples of BBEdit and GNU nano. You’re ultimate goal is to simply edit a text file to change configuration values. But you should be comfortable with whatever text editor you use.

#### Changing the PHP install time zone.

Out of the box, MAMP 2.1.4 is compiled to the original development team’s timezone of *Europe/Berlin* like so:

	date.timezone = "Europe/Berlin"

Unless you are actually in Berlin—or have a need to have your time zone set to Berlin—the `date.timezone` value should be changed to whatever your actual working time zone actually is like so:

	; date.timezone = "Europe/Berlin"
	date.timezone = "America/New_York"

Also, as you can see, I like to comment out changes that I make and leave them in place instead of deleting them. In general, you should get into the habit of commenting out small tweaks like this and leaving them in place for future reference instead of outright overwriting or deleting them. In a case like this it might seem superfluous, but remember: What might seem like a simple change now, might be the cause of a debugging issue months or years from now.

#### Adjusting resource limits and error reporting.

Server resources are idiosyncratic from server to server and from application to application. In general, the simplest resource limit adjustment you should make is to `memory_limit` so your PHP scripts don’t bomb out due to lack of memory. So look for the *Resource Limits* section of the PHP ini configuration that looks like this:

	max_execution_time = 30;
	max_input_time = 60;
	memory_limit = 32M;

And change the `memory_limit` value to something like `64M`:

	; memory_limit = 32M;
	memory_limit = 64M;

Once that is done, look for the *Error handling and logging* section of the PHP ini configuration and adjust `error_reporting` so you don’t loose your mind due to excessive warnings, errors and notices popping up when debugging. The `error_reporting` should look something like this:

	error_reporting = E_ALL

I generally change that to report anything but `strict` notices like so:

	; error_reporting = E_ALL
	error_reporting = E_ALL & ~E_STRICT

Also adjusting error reporting so you can actually see errors as they happen in the web browser—rather than having to read the output in the `php_error.log`—is a good idea. Just do a search for the `display_errors` which should look like this:

	display_errors = Off

And change that from `Off` to `On`:

	; display_errors = Off
	display_errors = On


Now with all that done, restart MAMP so the PHP ini configuration file is properly reloaded with the new settings. Remember, PHP is a module in Apache. So when you restart MAMP, you are restarting Apache and thus forcing the PHP ini configuration file to be reloaded.

#### Conclusion.

All of those settings basically make your life easier as a developer. But they don’t really show up as anything fancy in the web browser. So with that said, might as well start the life of your newly setup MAMP environment with a simple “Hello world!” program. Just create a basic `index.php` file like so:

	bbedit /Applications/MAMP/htdocs/index.php

And then copy in this short but sweet PHP script into that file:

	<?php

	echo "Hello world!";

	?>

Save the file and reload your browser. The text, “Hello world!” should be displayed in the brower. Which means now you’re done setting up your basic MAMP development environment!

***

*Developing With MAMP 2.1.4 • Part 1: Setting Up a Basic, Useful MAMP Environment (c) by Jack Szwergold; written February 16, 2014. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

[1]: http://us3.php.net/phpinfo "Info on phpinfo()."
[2]: http://www.barebones.com/products/bbedit/ "Bare Bones Software’s BBEdit page."
[3]: http://www.nano-editor.org "Info on GNO nano."
