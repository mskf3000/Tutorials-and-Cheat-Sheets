# Apache

By Jack Szwergold, September 19, 2015

***

***

#### Sundry Apache items.

Get a summary of the `VirtualHost` configurations for Apache:

	sudo apachectl -S

Getting service status with `watch` and `apache2ctl`:

	watch apache2ctl status

Getting service status with `watch` and `links`:

	watch -n 1 links -dump http://localhost/server-status

Getting service status with `elinks`:

	elinks http://localhost/server-status?refresh=1

Edit the default character set:

	sudo nano /etc/apache2/conf.d/charset

Getting `httpd` status with `fullstatus` on a CentOS/RedHat system:

	sudo httpd fullstatus

A nice one-liner to count unique IP addresses in an Apache log file:

    cat /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -n

Activate a virtual host config using `a2ensite`:

    sudo a2ensite sandbox.local.conf

Deactivate a virtual host config using `a2dissite`:

    sudo a2dissite sandbox.local.conf

Activate proxy related modules in Apache:

	sudo a2enmod proxy proxy_http

Deactivate proxy related modules in Apache:

	sudo a2dismod proxy proxy_http

#### Sundry Apache items on an Ubuntu/Debian system.

Get Apache version number:

	apache2 -v

Start, stop and control Apache on an Ubuntu/Debian system:

	sudo service apache2 status
	sudo service apache2 reload
	sudo service apache2 restart
	sudo service apache2 start
	sudo service apache2 stop

Find all running Apache processes on an Ubuntu/Debian system:

    ps -ef | grep apache2

If server is unresponsive to standard commands then manually kill off processes on an Ubuntu/Debian system:

	sudo pkill -9 apache2

Display all modules compiled into Apache:

    apache2 -l

The output would be something like this:

	Compiled in modules:
	  core.c
	  mod_log_config.c
	  mod_logio.c
	  prefork.c
	  http_core.c
	  mod_so.c

#### Sundry Apache items on a CentOS/RedHat system.

Get Apache version on a CentOS/RedHat system:

	httpd -v

Start, stop and control Apache on a CentOS/RedHat system:

	sudo /etc/init.d/httpd status
	sudo /etc/init.d/httpd reload
	sudo /etc/init.d/httpd restart
	sudo /etc/init.d/httpd start
	sudo /etc/init.d/httpd stop

Find all running Apache processes on a CentOS/RedHat system:

    ps -ef | grep httpd

If server is unresponsive to standard commands then manually kill off processes on a CentOS/RedHat system:

	sudo pkill -9 httpd

#### Installing and using `apachetop` to monitor Apache.

Installing & using 'apachetop' on an Ubuntu/Debian system.

	sudo aptitude install apachetop

Installing & using 'apachetop' on a CentOS/RedHat system.

	sudo yum install apachetop

Then run it as like this; note that it relies on the full path to the Apache access log:

    apachetop -r 2 -f /var/log/apache2/access.log

#### System configuration tweaks.

Memory Mapping (MMAP) stuff:

	#
	# EnableMMAP: Control whether memory-mapping is used to deliver
	# files (assuming that the underlying OS supports it).
	# The default is on; turn this off if you serve from NFS-mounted
	# filesystems.  On some systems, turning it off (regardless of
	# filesystem) can improve performance; for details, please see
	# http://httpd.apache.org/docs/2.2/mod/core.html#enablemmap
	#
	EnableMMAP Off
	
	#
	# EnableSendfile: Control whether the sendfile kernel support is
	# used to deliver files (assuming that the OS supports it).
	# The default is on; turn this off if you serve from NFS-mounted
	# filesystems.  Please see
	# http://httpd.apache.org/docs/2.2/mod/core.html#enablesendfile
	#
	EnableSendfile Off

#### Useful Apache rewrite rules.

Rewrite rule to force lowercase:

	RewriteEngine On
	RewriteMap insensitive int:tolower
	RewriteCond %{REQUEST_URI} [A-Z]
	RewriteRule (.*) ${insensitive:$1} [R=301,L]

Rewrite rule to redirect malware URLs with params:

	# 2012-07-28: Added to force malware URLs to finally go away.
	RedirectMatch 404 ^/new-nav/index.php
	RedirectMatch 404 ^/sites
	
	# 2012-09-06: Added to force more malware URLs to finally go away.
	RewriteCond %{QUERY_STRING} DrugO
	RewriteRule .* - [R=404]

Rewrite rule to block access based on referrer:

	RewriteEngine on
	# Options +FollowSymlinks
	RewriteCond %{HTTP_REFERER} example\.com [NC]
	RewriteRule .* - [F]
	
Rewrite rule to block access based on multiple referrers:

	RewriteEngine on
	# Options +FollowSymlinks
	RewriteCond %{HTTP_REFERER} example\.com [NC,OR]
	RewriteCond %{HTTP_REFERER} another_example\.com
	RewriteRule .* - [F]
	
Rewrite rule to redirect requests based on referrer:

	RewriteEngine On
	RewriteCond %{HTTP_REFERER} .*example.com.*$ [OR]
	RewriteCond %{HTTP_REFERER} .*another_example.com.*$ [OR]
	RewriteCond %{HTTP_REFERER} .*yet_another_example.com.*$
	RewriteRule ^(.*)$ http://www.preworn.com/

***

*Apache (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*