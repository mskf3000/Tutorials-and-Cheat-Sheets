## Developing With MAMP 2.1.4

By Jack Szwergold

### Part 2: Configuring Name-Based Virtual Hosts in MAMP

In this part of my tutorial I will show you how to use the power of Apache name-based virtual hosts to have more than one website document root served via your local MAMP install.

Why would you want more than one website document root served via your local MAMP install? Easy. As a developer, chances are you will be juggling lots of different projects. And while some projects can easily be self-contained into a separate directory of your MAMP install that is served off of the default `http://localhost:8888` site, some projects might require your codebase be served on an explicitly set document root. So instead of having to rip apart apart your MAMP setup and codebase each time you want to switch gears, Apache name-based virtual hosts allow you to have a more flexible MAMP development environment to begin with.

You can read up on [how Apache name-based virtual hosts work over here][1]. But it basically boils down to this: Traditional ip-based virtual hosts restrict you to having one website document root per IP address. But name-based virtual hosts allows Apache to recognize the different hostnames connected to a single IP address. Which means you can have multiple website document roots if you use name-based virtual hosts.

Which—for Mac users—means that you can utilize the way your Mac has a machine specific Bonjour MDNS hostname like `MY_MACHINE.local`—in addition to the traditional `localhost` hostname—to enable Apache name-based virtual hosts in your local MAMP install.

#### Let’s get started.

First determine what your local machine’s Bonjour MDNS hostname is by going to the command line and typing 'hostname' like so:

	hostname

Take a note of that hostname, which should be something like:

	MY_MACHINE.local

Got that? Good. Now, switch your attention to the `htdocs` root of MAMP.

#### Setting up another document root.

So now you are going to go to the `htdocs` root of MAMP and create a new directory called `test_host`:

	mkdir /Applications/MAMP/htdocs/test_host

With that done, create an `index.php` file for testing purposes in that directory:

	bbedit /Applications/MAMP/htdocs/index.php

And then copy in this short but sweet PHP script into that file:

	<?php

	echo "Hello world!";
	echo "<br />";
	echo "I am the root of a name-based virtual host.";
	echo "<br />";
	echo "My server name is: " . $_SERVER['SERVER_NAME'];

	?>

Note the use of `$_SERVER['SERVER_NAME']`. That will help you see which virtual host you are using to connect to the web server with.

#### Setting name-based virtual hosts in Apache.

So with that done, you’re now going to adjust the Apache configuration in your MAMP install to enable name-based virtual hosts. Open up the Apache `httpd.conf` file like so:

	bbedit /Applications/MAMP/conf/apache/httpd.conf

And add these lines to the bottom of the Apache `httpd.conf` file:

	NameVirtualHost *

	<VirtualHost *>
	 DocumentRoot "/Applications/MAMP/htdocs"
	 ServerName localhost
	 ServerAlias localhost
	</VirtualHost>

	<VirtualHost *>
	 DocumentRoot "/Applications/MAMP/htdocs/test_host"
	 ServerName *.local
	 ServerAlias *.local
	</VirtualHost>

The first `VirtualHost` config is pretty straight forward; it handles traffic going to `localhost` and sends it to `htdocs`. But note the second `VirtualHost` config. That one basically tells Apache to set the `DocumentRoot` to be `test_host` for connections going to any `ServerName` that has the `.local`—aka: `*.local`—top level domain.

Also note that I set `ServerAlias` in addition to `ServerName`. I do that out of habit. It could possibly be removed for this example, but I am so used to doing that for real world production servers—where one hostname can be an alias for another—I just leave that in place for my own reference.

#### Conclusion.

Now that you have a `test_host` directory in your main MAMP `htdocs` directory as well an adjusted Apache `httpd.conf` in your MAMP setup, you should be all set. So restart MAMP and go to this URL:

	http://MY_MACHINE.local:8888

And compare what you see in your browser to the to the basic/standard `localhost` setup URL:

	http://localhost:8888

The `index.php` page you’re viewing on `MY_MACHINE.local:8888` should be 100% different than what is served up from `localhost:8888` since `MY_MACHINE.local:8888` is serving the content from the `test_host` directory. While `localhost:8888` is still serving content from the main `htdocs` document root.

So thanks to the flexibility of name-based virtual hosts, Apache in your MAMP setup sees `MY_MACHINE.local:8888` and `localhost:8888` as two distinct and unique web servers. Which should make your life as web developer a bit easier.

***

*Developing With MAMP 2.1.4 • Part 2: Configuring Name-Based Virtual Hosts in MAMP (c) by Jack Szwergold; written February 17, 2014. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

[1]: http://httpd.apache.org/docs/2.2/vhosts/name-based.html "Apache HTTP Server • Name-based Virtual Host Support"
