## Nginx

By Jack Szwergold

Get the version of Nginx from the command line:

    nginx -v

If installed in a non-standard location—like `/opt`—run it like this:

    /opt/nginx/sbin/nginx -v

The output should be something like this:

    nginx version: nginx/1.4.1

#### Install Nginx on Ubuntu 14.04:

	sudo aptitude install nginx-full

Sundry service control commands:

	sudo service nginx restart
	sudo service nginx start
	sudo service nginx stop
	sudo service nginx status

#### Install `ngxtop`.

Install `ngxtop` to monitor realtime stats on the server like this. First install PIP like this:

	sudo aptitude install python-pip

Then install `ngxtop` like this:

	sudo pip install ngxtop

And then monitor the Nginx service like this:

	sudo ngxtop

An example of showing 200 top items and filtering out items that don’t begin with `/sockjs`:

	sudo ngxtop -n 200 --filter 'not request_path.startswith("/sockjs")'

#### Disable Nginx version number from header calls.

Open up the config file here:

	sudo nano /etc/nginx/nginx.conf

Look for this line:

	# server_tokens off;

And then uncomment it like this:

	server_tokens off;

Finally, restart Nginx like this:

	sudo service nginx restart

#### Sundry stuff.

Default Nginx page located here:

	/usr/share/nginx/html/index.html

Reverse proxy config for Nginx:

	map $http_upgrade $connection_upgrade {
	  default upgrade;
	  '' close;
	}
	
	server {
	
	  listen 80 default_server;
	  listen [::]:80 default_server ipv6only=on;
	
	  root /usr/share/nginx/html;
	  index index.html index.htm;
	
	  server_name vagrant.local;
	
	  # location / {
	  #   rewrite ^ https://$server_name$request_uri? permanent;
	  # }
	
	  location / {
	
	    proxy_pass http://127.0.0.1:8080;
	    proxy_http_version 1.1;
	    proxy_set_header Upgrade $http_upgrade;
	    proxy_set_header Connection $connection_upgrade;
	    proxy_set_header X-Forwarded-For $remote_addr;
	
	    # if ($uri != '/') {
	    #   expires 30d;
	    # }
	
	  }
	
	}

***

*Nginx (c) by Jack Szwergold; written on September 27, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
