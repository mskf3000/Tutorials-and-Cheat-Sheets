## Ubuntu - Let’s Encrypt Items

By Jack Szwergold

### The basics.

First add the official Let’s Encrypt `certbot` repo like this:

	sudo add-apt-repository ppa:certbot/certbot

Then update the repo data like this:
	
	sudo apt-get update
	
Now install `certbot` like this:
	
	sudo apt-get install python-certbot-apache

Install a certificate like this; each `-d` specifies a hostname that will be a part of the certificate:

    sudo certbot --apache -d preworn.com -d www.preworn.com

And with that, you should be done!

Just one last thing: Let’s Encrypt certificates last only 90 days. So the certificate process will auto-renew. But to test if the auto-renew process works on your machine, run this “dry run” command and pay attention to the output:

	sudo certbot renew --dry-run

Output should be clear and human readable. If there are errors it could be network related, firewall related or even related to the Apache config automatically redirecting from HTTP to HTTPS.

***

*Ubuntu - Let’s Encrypt Items (c) by Jack Szwergold; written on November 3, 2019. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*