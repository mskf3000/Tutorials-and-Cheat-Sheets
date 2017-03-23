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

	sudo aptitude install install python-pip

Then install `ngxtop` like this:

	sudo pip install ngxtop

And then monitor the Nginx service like this:

	sudo ngxtop

#### Disable Nginx version number from header calls.

Open up the config file here:

	sudo nano /etc/nginx/nginx.conf

Look for this line:

	# server_tokens off;

And then uncomment it like this:

	server_tokens off;

Finally, restart Nginx like this:

	sudo service nginx restart

***

*Nginx (c) by Jack Szwergold; written on September 27, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
