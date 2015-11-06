# Apache - Expires and Cache Control Headers

By Jack Szwergold, September 19, 2015

#### How to disable cache control and expires headers.

An example of disabling cache control headers:

	<IfModule mod_headers.c>
	  Header set Cache-control "max-age=1, no-cache, no-store"
	</IfModule>
	
An example of disabling expires headers:

	<IfModule mod_expires.c>
	  ExpiresActive On
	  ExpiresDefault A0
	</IfModule>

An example of disabling expires headers for a particular filename:

	<Files some_file_with_no_cache.jpg>
	  <IfModule mod_expires.c>
	    ExpiresActive on
	    ExpiresDefault A0
	  </IfModule>
	</Files>

An example of disabling expires headers to match a filename pattern:

	<Files *.jpg>
	  <IfModule mod_expires.c>
	    ExpiresActive on
	    ExpiresDefault A0
	  </IfModule>
	</Files>

#### Example cache control and expires headers.

An example of cache control headers:

	# 2014-06-02: Setting cache control headers.
	# VERY IMPORTANT: Do not use 'mod_expires' for any reason on a CDN origin.
	# MORE IMPORTANT: Always use 'Cache-control' headers with 's-maxage' for a CDN origin.
	<IfModule mod_headers.c>
	
	  # The default cache control setting is 3 minutes (3 * 60 = 180 seconds).
	  Header set Cache-control "s-maxage=7, max-age=180, public, must-revalidate, proxy-revalidate"
	
	  # 1 year (2592000 seconds in a month * 12 = 31104000 seconds in a year).
	  <FilesMatch "\.(pdf|flv|swf|mp4|mp3|f4v|mov)$">
	    Header set Cache-Control "s-maxage=7, max-age=31104000, public, must-revalidate, proxy-revalidate"
	  </FilesMatch>
	
	  # 1 month (86400 seconds in a day * 30 = 2592000 seconds in a month).
	  <FilesMatch "\.(xml|txt|css|js)$">
	    Header set Cache-Control "s-maxage=7, max-age=2592000, public, must-revalidate, proxy-revalidate"
	  </FilesMatch>
	
	  # 1 week (86400 seconds in a day * 7 = 604800 seconds in a year).
	  <FilesMatch "\.(ico|jpg|jpeg|png|gif)$">
	    Header set Cache-Control "s-maxage=7, max-age=604800, public, must-revalidate, proxy-revalidate"
	  </FilesMatch>
	
	  # 5 minutes (5 * 60 seconds = 600 seconds).
	  <FilesMatch "\.(html|htm|php)$">
	    Header set Cache-Control "s-maxage=7, max-age=600, public, must-revalidate, proxy-revalidate"
	  </FilesMatch>
	
	</IfModule>

An example of cache control and expires headers for an Amazon CloudFront CDN setup:

	# 2013-12-30: Setting cache control headers.
	<IfModule mod_headers.c>
	  # Header set Cache-control "max-age=300, no-cache, no-store"
	  Header set Cache-control "s-maxage=7, max-age=300, public, must-revalidate"
	  # Header set Cache-control "max-age=1, public, must-revalidate"
	</IfModule>
	
	# 2014-05-23: Setting expires headers.
	<IfModule mod_expires.c>
	  ExpiresActive On
	  ExpiresByType image/jpg "access plus 7 seconds"
	  ExpiresByType image/jpeg "access plus 7 seconds"
	  ExpiresByType image/gif "access plus 7 seconds"
	  ExpiresByType image/png "access plus 7 seconds"
	  ExpiresByType text/css "access plus 7 seconds"
	
	  ExpiresByType text/html "access plus 7 seconds"
	
	  ExpiresByType application/pdf "access plus 7 seconds"
	  ExpiresByType text/x-javascript "access plus 7 seconds"
	  ExpiresByType application/x-shockwave-flash "access plus 7 seconds"
	  ExpiresByType image/x-icon "access plus 7 seconds"
	  ExpiresDefault "access plus 7 seconds"
	</IfModule>

***

*Apache - Expires and Cache Control Headers (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*