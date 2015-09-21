# Cheat Sheet - Ruby - Apache Passenger Module Installation and Setup

By Jack Szwergold, September 20, 2015

Installing Passenger (mod_rack) to allow Apache2 to manage Ruby apps.

***

#### System prerequisites.

First, make sure you have a working LAMP (Linux, Apache, MySQL and PHP) stack running on the server. Also, make sure you have some version of Ruby installed.

These instructions cover installation of Passenger on systems where Ruby is *not* installed via RVM. Ruby versions 1.8, 1.9 and 2.0 and above are covered here.

With all of that covered, make sure the Apache development packages and Curl development packages are installed:

    sudo aptitude install apache2-dev libcurl4-openssl-dev

#### Install the Passenger Ruby GEM.

Install `passenger` on Ruby 1.8 or 1.9 like this:

	sudo gem install passenger -v "=4.0.53" --no-rdoc --no-ri

	sudo gem uninstall passenger --version 4.0.53

Install `passenger` on Ruby 2.0 like this:

	sudo gem install passenger:4.0.53 --no-rdoc --no-ri

#### Install the Passenger Apache Module.

Now run this binary—which was installed wth the Passenger Ruby GEM to get the Passenger Apache module installed:

    sudo /var/lib/gems/1.9.1/gems/passenger-4.0.53/bin/passenger-install-apache2-module

Just punch through the questions and let the process compile the module. Once done, move on.

### Creating a test Ruby app.

    sudo mkdir -p /var/www/sandbox.local/site

    sudo chown sysop:www-readwrite /var/www/sandbox.local/site

#### Create the `config.ru` file.

    nano /var/www/sandbox.local/site/config.ru

And this code goes in there:

	require './my_app'
	run MyApp.new

#### Create the `my_app.rb` file.

    nano /var/www/sandbox.local/site/my_app.rb

And this code goes in there:

	# my_app.rb
	class MyApp
	  def call env
	    [200, {"Content-Type" => "text/html"}, ["<h1>Hello, world!<\/h1>Ruby Version " + RUBY_VERSION + " on " + RUBY_PLATFORM]]
	  end
	end

#### Test if the Ruby app works by default, run it via 'rackup'.

    cd /var/www/sandbox.local/site/

    rackup config.ru

### Sample Apache configuration.

Add the following to the Apache virtual domain config and restart Apache.

	<VirtualHost *:80>
	  DocumentRoot /var/www
	
	  CustomLog ${APACHE_LOG_DIR}/access.log combined
	  ErrorLog ${APACHE_LOG_DIR}/error.log
	
	  # Possible values include: debug, info, notice, warn, error, crit, alert, emerg.
	  LogLevel warn
	
	  Alias /phpmyadmin /usr/share/phpmyadmin/
	  Alias /munin /var/cache/munin/www
	
	  RedirectMatch 404 /(builds|configs|content)(/|$)
	
	  # 2013-11-04: Including common items in a common file for ssl & non-ssl.
	  include /etc/apache2/sites-available/common.conf
	
	  # 2014-03-12: Including common ModSecurity related items.
	  # include /etc/apache2/sites-available/common_mod_security.conf
	
	  <IfModule !mod_passenger.c>
	    LoadModule passenger_module /usr/local/lib/ruby/gems/2.1.0/gems/passenger-4.0.53/buildout/apache2/mod_passenger.so
	  </IfModule>
	  <IfModule mod_passenger.c>
	    PassengerRoot /usr/local/lib/ruby/gems/2.1.0/gems/passenger-4.0.53
	    PassengerDefaultRuby /usr/local/bin/ruby
	
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
	
	  <IfModule mod_passenger.c>
	    PassengerEnabled on
	    PassengerAppRoot /var/www/sandbox.local/site
	    RackBaseURI /
	  </IfModule>
	
	</VirtualHost>

***

*Cheat Sheet - Ruby - Apache Passenger Module Installation and Setup (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
