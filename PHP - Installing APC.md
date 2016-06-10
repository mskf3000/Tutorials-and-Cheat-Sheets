## PHP - Installing APC

By Jack Szwergold, October 5, 2015

APC (Alternative PHP Cache) is an opcode (operation code) cache that can improve PHP performance and decrease RAM usage dramatically. While it works great when it works, some PHP applications might have slight flaws in them that will cause APC to crash sporadically. So if you intend to use APC, be sure to test your code on an APC-enabled developer’s environment before deploying APC—and the updated code—to production.

### Installing APC (Alternative PHP Cache) from Pear/Pecl source.

First, make sure some core PHP development packages are installed like this:

    sudo aptitude install php5-dev libpcre3-dev

Then, run Pear upgrade to get everything up to date:

	sudo pear upgrade pear

Now install APC via Pecl like this:

    sudo pecl install -f apc

Whatever question prompts you get, just punch through it and wait for the compile/install process to complete.

Once done open up the the `apc.ini` config file in Apache like this:

	sudo nano /etc/php5/conf.d/apc.ini

And add these contents to the file:

	;extension=apc.so
	
	apc.enabled = 1
	apc.shm_segments = 1
	apc.shm_size = 256M
	apc.ttl = 3600
	apc.user_ttl = 3600
	apc.gc_ttl = 3600
	apc.num_files_hint = 1024
	apc.mmap_file_mask = /tmp/apc.XXXXXX
	apc.enable_cli = 0

Note that this initial config actually disables APC via commenting out `;extension=apc.so`. That’s because you really don’t want APC running unless you are 100% sure you PHP code will play nicely with it. If you want to enable it, just change that one line to `extension=apc.so` and restart Apache to load the module.

### Some APC (Alternative PHP Cache) example configurations.

This one seems okay but is really a bit flawed from real world tests:

	extension=apc.so
	
	apc.enabled = 1
	apc.shm_segments = 1
	apc.shm_size = 32M
	apc.ttl = 3600
	apc.user_ttl = 3600
	apc.num_files_hint = 1024
	apc.mmap_file_mask = /tmp/apc.XXXXXX
	apc.enable_cli = 0

This one is newer and more stable than the flawed config:

	extension=apc.so
	
	apc.enabled = 1
	apc.shm_segments = 1
	apc.shm_size = 32M
	apc.ttl = 0
	apc.user_ttl = 600
	apc.gc_ttl = 600
	apc.num_files_hint = 1024
	apc.mmap_file_mask = /tmp/apc.XXXXXX
	apc.enable_cli = 0
	apc.max_file_size = 1M

This one is even newer, more stable and more optimized than the other one:

	extension=apc.so
	
	apc.enabled = 1
	apc.shm_segments = 1
	apc.shm_size = 32M
	apc.ttl = 0
	apc.user_ttl = 180
	apc.gc_ttl = 180
	apc.num_files_hint = 1024
	apc.mmap_file_mask = /dev/zero
	apc.enable_cli = 0
	apc.max_file_size = 1M
	apc.num_files_hint = 512

#### Setup the APC monitoring script.

Copy the PHP monitoring script from the APC install to the web directory you want it installed in like this:

	sudo cp /usr/share/doc/php-apc/apc.php.gz /var/www/apc.php.gz

Now decompress the archives:

	sudo gzip -d /var/www/apc.php.gz

Edit the `apc.php`:

	sudo nano /var/www/apc.php

Search for `ADMIN_USERNAME` and `ADMIN_PASSWORD` and change the username and password values to whatever you feel it should be:

	defaults('ADMIN_USERNAME','username'); // Admin Username
	defaults('ADMIN_PASSWORD','password'); // Admin Password - CHANGE THIS TO ENABLE!!!

Then point your web browser to the following URL to see it in action:

    http://sandbox.local/apc.php

***

*PHP - Installing APC (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
