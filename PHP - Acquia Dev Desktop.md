## PHP - Acquia Dev Desktop

By Jack Szwergold

## MySQL Specific Stuff

Acquia Dev Desktop MySQL Binary Path

	/Applications/DevDesktop/mysql/bin

Default Acquia Dev Desktop MySQL host and port:

	127.0.0.1:33067

Acquia Dev Desktop MySQL runs with a password-less `root` account so a simple connection would be something like this:

	mysql -uroot --host=127.0.0.1 --port=33067

Or expclicitly use the bundled MySQL client binary like this:

	/Applications/DevDesktop/mysql/bin/mysql -uroot --host=127.0.0.1 --port=33067

So a database can be imported like this:

	mysql -uroot --host=127.0.0.1 --port=33067 some_website_db < /path/to/databases/some_website_db.sql

And a GZipped directory can be imported like this:

	gunzip < /path/to/databases/some_website_db.sql.gz | mysql -uroot --host=127.0.0.1 --port=33067 some_website_db

***

*PHP - Acquia Dev Desktop (c) by Jack Szwergold; written on December 13, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
