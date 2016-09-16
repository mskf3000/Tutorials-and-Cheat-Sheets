## Mac OS X - MAMP Related Items

By Jack Szwergold

This is how you can setup named virtual hosts on MAMP. First open up the `httpd.conf` file like this:

    nano /Applications/MAMP/conf/apache/httpd.conf

Add this to the bottom of the file. Requests to a `localhost` request would go to one virtual host. Requests to any host with a `*.local` extension go to the other virtual host.

	NameVirtualHost *
	
	<VirtualHost *>
	  DocumentRoot "/Applications/MAMP/htdocs"
	  ServerName localhost
	  ServerAlias localhost
	</VirtualHost>
	
	<VirtualHost *>
	  DocumentRoot "/Applications/MAMP/htdocs/something/else"
	  ServerName *.local
	  ServerAlias *.local
	</VirtualHost>

### Dealing with `DYLD_LIBRARY_PATH` issues connected to MAMP and ImageMagick.

Sometimes binaries compiled with dynamic library requirements choke when being called via `exec()` or `shell_exec()` in MAMP. I first noticed this when having issues with ImageMagick `convert` being called via the `exec()` with errors showing up via `stderr` instead of the standard PHP error log; massive pain in the ass:

    tail -f -n 200 /var/log/system.log

This is due to MAMP setting a `DYLD_LIBRARY_PATH` environment variable. Unsure what is lost by disabling this, but I was able to fix ImageMagick interaction with MAMP by neutering it like this.

First open up the MAMP specific, `envvars` file like this:

    nano /Applications/MAMP/Library/bin/envvars

Then find the line near the bottom that reads like this:

    export DYLD_LIBRARY_PATH

And comment it out like this:

    #export DYLD_LIBRARY_PATH

Finally restart the MAMP servers and all should be good as far as `exec()` and programs that require dynamic libraries like ImageMagick.

### Adjusting the PHP timezone.

Since MAMP is developed in Germany, the default time zone is set to `Europe/Berlin`. To make things easier when debugging things if you are not in Germany is to adjust the timezone like this. First, open up the MAMP specific `php.ini` file like this:

    nano /Applications/MAMP/bin/php/php5.6.10/conf/php.ini

Find this chunk of configuration code:

	; Defines the default timezone used by the date functions
	; Will be changed by MAMP to system timezone
	date.timezone = "Europe/Berlin"

Then adjust it like this; which in this case switches the timezone to `America/New_York`:

	; Defines the default timezone used by the date functions
	; Will be changed by MAMP to system timezone
	; date.timezone = "Europe/Berlin"
	date.timezone = "America/New_York"

With that done, save the file, restart MAMP and the timezone for things like PHP error logs will now be properly set to the `America/New_York` timezone.

***

*Mac OS X - MAMP Related Items (c) by Jack Szwergold; written on October 7, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*