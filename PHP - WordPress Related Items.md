# Cheat Sheet - PHP - WordPress Related Items

By Jack Szwergold, October 16, 2015

#### Resetting a user password directly in the WordPress MySQL database.

If you need to reset a user’s password, but cannot do so via WordPress’s web interface, you can change it directly in the MySQL database using a query like this:

	UPDATE wp_users SET user_pass = MD5('[new password]') WHERE ID = [user id];

Just change the value of `[new password]` to match the new password you wish to use and change `[user id]` to match the user ID of the user this is being done for.
	
#### Fixing WordPress FTP permissions.

Sometimes WordPress will bizarrely ask for FTP credentials when a user in the web interface attempts to install a plug-in. The reality is you don’t need FTP credentials and this message is a bit ridiculous.

So to fix that issue, you need to change ownership of the WordPress install to match the Apache web user. For example, let’s say that the Apache web server is being run by the user `www-data` just run this `chown` command to solve the issue:

	sudo chown -R www-data /var/www/www.example.com

Note that this oddball FTP issue has 100% nothing to do with group permissions. Even if the user running Apache has group access to write to the WordPress install, this ridiculous FTP credential related error will pop up.

#### Allocating more memory to WordPress PHP processes.

If you need to allocate more memory to the PHP processes that WordPress initiates, just open up `wp-config.php` and add a `WP_MEMORY_LIMIT` line like this:
	
	define('WP_MEMORY_LIMIT', '64M');

But be careful with what you set here. Remember that this setting is a per process value and each time Apache starts, Apache will launch multiple child processes.

So if—for example—Apache launches 32 processes and all 32 processes are set to use 64M a piece, and 32 requests to Apache are made simultaneously. That would mean that the server will be using 2GB of memory when managing Apache connections. It’s actually not as simple as that since Apache is a per-request based service, but always keep memory usage in mind and don’t just set a value like this to something like 256MB unless your server can handle 8GB of memory usage from a flood of traffic.

#### Relocating a WordPress install to a new URL.

If you are planning on migrating a WordPress install from one location to another—either another server or a different location on the same server—you need to open up `wp-config.php` on the new install location and add a `RELOCATE` line like this:

     define('RELOCATE', true);

With that added, just visit the new WordPress install location, login to the admin area and make sure all looks good. Once you’re confident it all looks good, just remove that `RELOCATE` line from `wp-config.php` or just set it to `false` like this:

     define('RELOCATE', false);

Using `RELOCATE` forces the WordPress install to rewrite many default internal settings. Technically a server could run with `RELOCATE` set to `true`, but it’s performance would be slowed down by the things `RELOCATE` attempts to do with each page request. So you ultimately don’t want that kind of stuff happening for an extended period of time on a production server.

***

*Cheat Sheet - PHP - WordPress Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*