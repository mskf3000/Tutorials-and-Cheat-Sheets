# Cheat Sheet - Ruby - Apache Passenger Module Installation and Setup

By Jack Szwergold, September 20, 2015

This tutorial outlines how to install Passenger (`mod_passenger`) to allow an Apache to manage Ruby applications.

Why? Easy. Apache is far more robust and flexible when it comes to web server configuration than most Ruby servers. Passenger allows Ruby applications to leverage this flexibility easily and without having to play around with deeper Ruby web server specifics.

***

### System prerequisites.

First, make sure you have a working LAMP (Linux, Apache, MySQL and PHP) stack running on the server. Also, make sure you have some version of Ruby installed.

These instructions cover installation of Passenger on systems where Ruby is *not* installed via RVM. Ruby versions 1.8, 1.9 and 2.0 and above are covered here.

With all of that covered, make sure the Apache development packages and Curl development packages are installed:

    sudo aptitude install apache2-dev libcurl4-openssl-dev

### Install the Passenger Ruby GEM.

Install `passenger` on Ruby 1.8.x or 1.9.x like this:

	sudo gem install passenger -v "=4.0.59" --no-rdoc --no-ri

Uninstall `passenger` on Ruby 1.8.x or 1.9.x like this:

	sudo gem uninstall passenger --version 4.0.59

Install `passenger` on Ruby 2.x.x like this:

	sudo gem install passenger:4.0.59 --no-rdoc --no-ri

Unnstall `passenger` on Ruby 2.x.x like this:

	sudo gem uninstall passenger --version 4.0.59

### Install the Passenger Apache Module.

Now run this binary—which was installed with the Passenger Ruby GEM to get the Passenger Apache module installed:

    sudo /usr/local/bin/passenger-install-apache2-module

That is really a symbolic link to the actual binary installed with the Ruby GEM. So if somehow that symbolic link doesn’t work, just run it directly like this:

    sudo /var/lib/gems/2.2.0/gems/passenger-4.0.59/bin/passenger-install-apache2-module

Just punch through the questions and let the process compile the module. Once done, move on.

### Create a simple Ruby test application.

With Passenger installed, we should set up a simple Ruby test application to test the setup with. First step is to create a simple Apache virtual host directory structure if one doesn’t exist already:

    sudo mkdir -p /var/www/sandbox.local/site

Now adjust the permissions to the directory like this:

    sudo chown sysop:www-readwrite /var/www/sandbox.local/site

With that done, let’s create the simple Ruby application starting with the Ruby config file.

#### Create the `config.ru` file.

The first thing to do is create a Ruby config file (`config.ru`) like this:

    sudo nano /var/www/sandbox.local/site/config.ru

And this Ruby configuration goes in there:

	require './my_app'
	run MyApp.new

Save it and change the permissions like this:

    sudo chown sysop:www-readwrite /var/www/sandbox.local/site/config.ru

With that done, let’s create the actual Ruby application code.

#### Create the `my_app.rb` file.

Now create the actual Ruby application file (`my_app.rb`) like this:

    nano /var/www/sandbox.local/site/my_app.rb

And this code goes in there:

	# my_app.rb
	class MyApp
	  def call env
	    [200, {"Content-Type" => "text/html"}, ["<h1>Hello world!<\/h1>Ruby Version " + RUBY_VERSION + " on " + RUBY_PLATFORM]]
	  end
	end

Save it and change the permissions like this:

    sudo chown sysop:www-readwrite /var/www/sandbox.local/site/my_app.rb

With that done, now let’s test the Ruby app manually to see if that works.

#### Test if the Ruby app itself works by running it via `rackup`.

So now, we just need to confirm that the simple Ruby application we just created works. So go into the working directory for the application like this:

    cd /var/www/sandbox.local/site/

And run the `rackup` command on that Ruby config file (`config.ru`) like this:

    rackup config.ru

The running output should be something like this:

	[2015-09-20 21:30:11] INFO  WEBrick 1.3.1
	[2015-09-20 21:30:11] INFO  ruby 1.9.3 (2011-10-30) [x86_64-linux]
	[2015-09-20 21:30:11] INFO  WEBrick::HTTPServer#start: pid=21724 port=9292

And you can test the application via a web browser by going to this URL:

    http://sandbox.local:9292

The returned output from the server should be something like this:

> **Hello world!**
>
> Ruby Version 1.9.3 on x86_64-linux

If somehow your server’s firewall is restricting traffic and you can’t access services on port `9292`, then just launch another SSH session and test the URL with `curl` like this:

    curl http://localhost:9292

The returned output from the server when using `curl` should be something like this:

> `<h1>Hello world!</h1>Ruby Version 1.9.3 on x86_64-linux`

Yes, that’s raw HTML you are seeing with `curl`, but the results still look good; the simple application is doing what it should be doing.

And if the application is working, just quit it and now move onto the real reason we’re doing all of this: To get Apache working with the Passenger module (`mod_passenger.so`).

### Sample Apache Passenger module configuration.

Now with all of that done, open up the Apache configuration file you want to add Passenger support to; such as this `default` one:

    sudo nano /etc/apache2/sites-available/default

And then add then Passenger (`mod_passenger`) configuration stuff to the virtual host configuration file:

	# Check if Passenger is loaded. And if it isn’t? Load it.
	<IfModule !mod_passenger.c>
	  LoadModule passenger_module /var/lib/gems/2.2.0/gems/passenger-4.0.59/buildout/apache2/mod_passenger.so
	</IfModule>
	
	# Passenger configuration items.
	<IfModule mod_passenger.c>
	  PassengerRoot /var/lib/gems/2.2.0/gems/passenger-4.0.59
	  PassengerDefaultRuby /usr/bin/ruby1.9.1
	
	  PassengerSpawnMethod smart-lv2
	  PassengerBufferResponse on
	  PassengerPoolIdleTime 120
	  PassengerMaxPoolSize 10
	  PassengerMaxInstancesPerApp 10
	  PassengerMaxRequests 2000
	  # PassengerStatThrottleRate 300
	  # PassengerStatThrottleRate 60
	  PassengerStatThrottleRate 0
	  PassengerEnabled off
	
	</IfModule>
	
	# And more specific Passenger configuration items.
	<IfModule mod_passenger.c>
	  PassengerEnabled on
	  PassengerAppRoot /var/www/sandbox.local/site
	  RackBaseURI /
	</IfModule>

Restart the Apache server like this:

    sudo service apache2 restart

And open up a web browser and head to this URL:

    http://sandbox.local

The returned output from the server should be something like this:

> **Hello world!**
>
> Ruby Version 1.9.3 on x86_64-linux

And you are now good to go!

***

*Cheat Sheet - Ruby - Apache Passenger Module Installation and Setup (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
