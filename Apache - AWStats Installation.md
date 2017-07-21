## Apache - AWStats Installation

By Jack Szwergold

### Installing AWStats.

Go into the `/usr/share` directory:

    cd /usr/share

Grab a compressed archive of `awstats-7.3.tar.gz` from an official AWStats source site:

	sudo curl -O -L http://prdownloads.sourceforge.net/awstats/awstats-7.3.tar.gz

Next, decompress the archive like this:

	sudo tar -xf awstats-7.3.tar.gz

And delete the remnant `awstats-7.3.tar.gz` archive:
	
	sudo rm /usr/share/awstats-7.3.tar.gz

### Configuring Apache for AWStats.
	
#### How to set it up in Ubuntu 12.04: 

Now let’s create our own `awstats.conf` like this:

	sudo nano /etc/apache2/conf.d/awstats.conf

Here is an example of a basic, non-secure Apache config for AWStats:

	# The default method which doesn't allow directory indexing
	# ScriptAlias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	
	# Alias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	# Alias /awstatsclasses /usr/share/awstats-7.3/wwwroot/classes/
	# Alias /awstatscss /usr/share/awstats-7.3/wwwroot/css/
	# Alias /awstatsicons /usr/share/awstats-7.3/wwwroot/icon/
	# Alias /icon /usr/share/awstats-7.3/wwwroot/icon/
	
	# Modified method that allows indexing
	<Directory "/usr/share/awstats-7.3/wwwroot/cgi-bin">
	  AddHandler cgi-script cgi pl
	  Options ExecCGI
	</Directory>

	<Directory "/usr/share/awstats-7.3/wwwroot/cgi-bin">
	  AddHandler cgi-script cgi pl
	  Options ExecCGI
	</Directory>

Here is an example of a basic, secure Apache config for AWStats. Note the `Allow from` exceptions; feel free to add any IP address you wish to bypass that secure setup to that list:

	# The default method which doesn't allow directory indexing
	# ScriptAlias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	
	# Alias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	# Alias /awstatsclasses /usr/share/awstats-7.3/wwwroot/classes/
	# Alias /awstatscss /usr/share/awstats-7.3/wwwroot/css/
	# Alias /awstatsicons /usr/share/awstats-7.3/wwwroot/icon/
	# Alias /icon /usr/share/awstats-7.3/wwwroot/icon/
	
	# Modified method that allows indexing
	<Directory "/usr/share/awstats-7.3/wwwroot/cgi-bin">
	  AddHandler cgi-script cgi pl
	  Options ExecCGI
	</Directory>
	
	<Directory "/usr/share/awstats-7.3/wwwroot">
	  Options FollowSymLinks
	  AllowOverride All
	
	  AuthName "AWStats Access"
	  AuthType Basic
	  require valid-user
	  AuthUserFile /etc/apache2/htpasswd_awstats
	
	  Order Deny,Allow
	  Deny from all
	  Allow from 127.0.0.1 ::1
	  Allow from localhost
	  Allow from 192.168
	  Allow from 10
	  Satisfy Any
	
	</Directory>

#### How to set it up in Ubuntu 14.04: 

Now let’s create our own `awstats.conf` like this:

    sudo nano /etc/apache2/conf-available/awstats.conf

Here is an example of a basic, non-secure Apache config for AWStats:

	# The default method which doesn't allow directory indexing
	# ScriptAlias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	
	# Alias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	# Alias /awstatsclasses /usr/share/awstats-7.3/wwwroot/classes/
	# Alias /awstatscss /usr/share/awstats-7.3/wwwroot/css/
	# Alias /awstatsicons /usr/share/awstats-7.3/wwwroot/icon/
	# Alias /icon /usr/share/awstats-7.3/wwwroot/icon/
	
	# Modified method that allows indexing
	<Directory "/usr/share/awstats-7.3/wwwroot/cgi-bin">
	  AddHandler cgi-script cgi pl
	  Options ExecCGI
	</Directory>

Here is an example of a basic, secure Apache config for AWStats. Note the `Allow from` exceptions; feel free to add any IP address you wish to bypass that secure setup to that list:

	# The default method which doesn't allow directory indexing
	# ScriptAlias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	
	# Alias /awstats /usr/share/awstats-7.3/wwwroot/cgi-bin
	# Alias /awstatsclasses /usr/share/awstats-7.3/wwwroot/classes/
	# Alias /awstatscss /usr/share/awstats-7.3/wwwroot/css/
	# Alias /awstatsicons /usr/share/awstats-7.3/wwwroot/icon/
	# Alias /icon /usr/share/awstats-7.3/wwwroot/icon/
	
	# Modified method that allows indexing
	<Directory "/usr/share/awstats-7.3/wwwroot/cgi-bin">
	  AddHandler cgi-script cgi pl
	  Options ExecCGI
	</Directory>
	
	<Directory "/usr/share/awstats-7.3/wwwroot">
	  Options FollowSymLinks
	  AllowOverride All
	
	  AuthName "AWStats Access"
	  AuthType Basic
	  require valid-user
	  AuthUserFile /etc/apache2/htpasswd_awstats
	
	  Require all denied
	  Require ip 127.0.0.1 ::1
	  Require host localhost
	  Require ip 192.168
	  Require ip 10
	
	</Directory>

With that done, be sure to enable the AWStats Apache module like this:

	sudo a2enconf awstats

***

And if you are using the secure setup, be sure to setup the `/etc/apache2/htpasswd_awstats` that config refers to like this:

    sudo htpasswd -c /etc/apache2/htpasswd_awstats awstats

That command will initiate the process to create an `htpasswd` for a user named `awstats` in the file named `htpasswd_awstats`. When prompted, enter whatever password you would like to use.

### Installing `awstatstotals.php` as the index page for AWStats.

By default, AWStats has no formal `index.php` set which is a pain in the ass. But you can install `awstatstotals.php` as a nice index for the install like this.

First grab a copy of `awstatstotals.php` and place it in your user’s home directory. Then move it—and rename it to `index.php`—into the main AWStats code directory like this:

	sudo mv ~/awstatstotals.php /usr/share/awstats-7.3/wwwroot/cgi-bin/index.php

Then change the permissions of the file so it’s world readable:

	sudo chmod a+r /usr/share/awstats-7.3/wwwroot/cgi-bin/index.php

### Create the `data/` directory.

Just create the `data/` directory like this:

	sudo mkdir /usr/share/awstats-7.3/wwwroot/data

If you need to double-check anything in the core AWStats install, just check these directories:

	sudo ls -la /usr/share/awstats-7.3/wwwroot/data
	sudo ls -la /usr/share/awstats-7.3/wwwroot/cgi-bin

### Create a config for your web server.

Now, create a config file for your web server by copying the `awstats.model.conf` to whatever the name of your server is. Like `awstats.www.example.com.conf` for `www.example.com`:

    sudo cp /usr/share/awstats-7.3/wwwroot/cgi-bin/awstats.model.conf /usr/share/awstats-7.3/wwwroot/cgi-bin/awstats.www.example.com.conf

Once that is done, open up the config like this:

	sudo nano /usr/share/awstats-7.3/wwwroot/cgi-bin/awstats.www.example.com.conf

And adjust `LogFile`, `SiteDomain` and `DirData` to match your server setup and AWStats setup:

	LogFile="/var/log/apache2/www.example.com.access.log"
	SiteDomain="www.example.com"
	DirData="/usr/share/awstats-7.3/wwwroot/data"

### Adjust ownership and permissions of the install.

If somehow AWStats chokes due to ownership issues, just run this `chown` command:

	sudo chown root:root -R /usr/share/awstats-7.3

And if you have issues with the AWStats data directory, be sure to run a `chmod` command to make all data in it writable by the group:

	sudo chmod g+w /usr/share/awstats-7.3/wwwroot/data

### Updating AWStats data.

With everything configured and set

	sudo /usr/share/awstats-7.3/wwwroot/cgi-bin/awstats.pl -config=www.example.com -update

Wait for AWStats to do it’s thing and when it’s done, check the data out in the web interface.

If all looks good, you can add that command as a cron job to get AWStats to do regular updates. This crontab entry would run that command every 30 minutes:

	*/30 * * * * /usr/share/awstats-7.3/wwwroot/cgi-bin/awstats.pl -config=www.example.com -update >/dev/null 2>&1

### Adding GeoIP lookup support to AWStats.

Before anything, you should have core GeoIP lookup items installed on your system already such as the `geoiplookup` binary and related database. Once that is done, you can follow the rest of these instructions.

#### Installing Perl support for GeoIP stuff.

To add GeoIP lookup support to AWStats, you first need to install some Perl modules via CPAN like this. First go into your user’s home directory like this:

	cd ~/

Now install CPANminus like this:

	sudo aptitude install cpanminus

With that done, install all of the GeoIP related modules like this:

	sudo cpanm -i -f YAML Geo::IP Geo::IPfree Geo::IP::PurePerl URI::Escape Net::IP Net::DNS Net::XWhois Time::HiRes Time::Local

#### Tweaks the AWStats configuration files.

Now find all of your AWStats config files using this simple `ls` command:

	ls -la /usr/share/awstats-7.*/wwwroot/cgi-bin/aw*.conf

And adjust `ExtraTrackedRowsLimit`, `DNSLookup`, `SkipHosts` and `LoadPlugin` values like this.

Adjust `ExtraTrackedRowsLimit` from `500` to `5000`:

	#ExtraTrackedRowsLimit=500
	ExtraTrackedRowsLimit=5000

Disable `DNSLookup` by setting it to `0`:
	
	#DNSLookup=1
	DNSLookup=0

Disable `SkipHosts` by setting it to an empty value:

	SkipHosts=""
	#SkipHosts="127.0.0.1"

Finally set the `LoadPlugin` values for various GeoIP related items like this. Note that the data directory paths should be set to match your local setup:

	#LoadPlugin="geoip GEOIP_STANDARD"
	LoadPlugin="geoip GEOIP_STANDARD /usr/local/share/GeoIP/GeoIP.dat"
	LoadPlugin="geoip_city_maxmind GEOIP_STANDARD /usr/local/share/GeoIP/GeoIPCity.dat"
	LoadPlugin="geoip_org_maxmind GEOIP_STANDARD /usr/local/share/GeoIP/GeoIPASNum.dat"

### Sundry AWStats stuff.

#### Directory ownership and permissions.

***NOTE:** I have a feeling these ownership and permissions notes have to do with the default Ubuntu install of AWStats 6.95, but unsure. Leaving here for reference.*

Here are the core parent AWStats directories:

	sudo ls -la /etc/awstats
	sudo ls -la /usr/share/awstats
	sudo ls -la /usr/share/doc/awstats-7.03
	sudo ls -la /var/lib/awstats

If ownership needs to be adjusted, just run these commands:

	sudo chown awstats:awstats -R /etc/awstats
	sudo chown awstats:awstats -R /usr/share/awstats
	sudo chown awstats:awstats -R /usr/share/doc/awstats-7.03
	sudo chown awstats:awstats -R /var/lib/awstats

If permissions need to be adjusted, just run these commands:

	sudo chmod 754 -R /etc/awstats
	sudo chmod 754 -R /etc/awstats/data
	sudo chmod 754 -R /usr/share/awstats
	sudo chmod 754 -R /usr/share/doc/awstats-7.03
	sudo chmod 754 -R /var/lib/awstats
	
As well as these commands:

	sudo chmod 755 /etc/awstats
	sudo chmod 755 /etc/awstats/data
	sudo chmod 755 /usr/share/awstats
	sudo chmod 755 /usr/share/doc/awstats-7.03
	sudo chmod 755 /var/lib/awstats

And this command:
	
	sudo chmod 775 /usr/share/awstats/wwwroot/cgi-bin/

#### Known icons missing from AWStats.

Some default icons in AWStats are missing. And these are them:

	conf.png
	csv.png
	document.png
	dtd.png
	flv.png
	fon.png
	package.png
	runtime.png
	swf.png
	vbs.png
	xsl.png

#### URL with query stuff.

If you want AWStats to process data on URLs with query strings, just set `URLWithQuery` to `1`:

	# Keep or remove the query string to the URL in the statistics for individual
	# pages. This is primarily used to differentiate between the URLs of dynamic
	# pages. If set to 1, mypage.html?id=x and mypage.html?id=y are counted as two
	# different pages.
	# Warning, when set to 1, memory required to run AWStats is dramatically
	# increased if you have a lot of changing URLs (for example URLs with a random
	# id inside). Such web sites should not set this option to 1 or use seriously
	# the next parameter URLWithQueryWithOnlyFollowingParameters (or eventually
	# URLWithQueryWithoutFollowingParameters).
	# Change : Effective for new updates only
	# Possible values:
	# 0 - URLs are cleaned from the query string (ie: "/mypage.html")
	# 1 - Full URL with query string is used     (ie: "/mypage.html?p=x&q=y")
	# Default: 0
	#
	# URLWithQuery=0
	URLWithQuery=1

#### Config to report data on media files.

Append this custom report configuration to your website’s core configuration if you want to get AWStats to process data on various image, movie, audio and related media files:

	# 2013-12-30: Adding extra section to track media.
	ExtraSectionName1="Hits on Images/Movies/Media"
	ExtraSectionCodeFilter1="200 304"
	ExtraSectionCondition1="URL,\.(png|gif|jpe?g|bmp|ico|mpg|mpeg|mp4|mov|flv|f4v|swf|mp3|aif?f|tif?f|xml)$"
	ExtraSectionFirstColumnTitle1="Image"
	ExtraSectionFirstColumnValues1="URL,^(\/.*\.(png|gif|jpe?g|bmp|ico|mpg|mpeg|mp4|mov|flv|f4v|swf|mp3|aif?f|tif?f|xml))$"
	ExtraSectionFirstColumnFormat1="<A HREF='http://cdn-home.guggenheim.org%s' TARGET='_blank'>%s</A>"
	ExtraSectionStatTypes1=HBL
	ExtraSectionAddSumRow1=1
	MaxNbOfExtra1=25
	MinHitExtra1=1

#### Config to report data on media file hot-linking.

Append this custom report configuration to your website’s core configuration if you want to get AWStats to process data on sites that might be hot-linking to your site’s assets:

	# 2013-12-30: Adding extra section to track hot-linking.
	ExtraSectionName2="Hot-linking pages"
	ExtraSectionCodeFilter2="200 304"
	ExtraSectionCondition2="URL,\.mpeg$||URL,\.mpg$||URL,\.avi$||URL,\.jpg$||URL,\.gif$||URL,\.png$||URL,\.bmp$||URL,\.jpeg||URL,\.mov||URL,\.flv||URL,\.f4v||URL,\.swf||URL,\.tif||URL,\.tiff||URL,\.xml$"
	ExtraSectionFirstColumnTitle2="Referrer"
	ExtraSectionFirstColumnValues2="REFERER,^(?!http:\/\/cdn-home.guggenheim.org)http:\/\/(.*)$"
	ExtraSectionFirstColumnFormat2="<a href='http://%s' target='_blank'>%s</a>"
	ExtraSectionStatTypes2=HBL
	ExtraSectionAddSumRow2=1
	MaxNbOfExtra2=25
	MinHitExtra2=1

***

*Apache - AWStats Installation (c) by Jack Szwergold; written on October 16, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*