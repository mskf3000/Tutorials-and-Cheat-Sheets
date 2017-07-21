## PHP - Drush

By Jack Szwergold

### Drush related stuff.

* **Drush Git repo**: https://github.com/drush-ops/drush
* **Drush install info**: http://www.drush.org/en/master/install/

Download latest stable release using the code below or browse to github.com/drush-ops/drush/releases:

	php -r "readfile('https://s3.amazonaws.com/files.drush.org/drush.phar');" > drush

Or use this command to get a development release:

    php -r "readfile('https://s3.amazonaws.com/files.drush.org/drush-unstable.phar');" > drush
	
Test your install.

	php drush core-status
	
Make `drush` executable as a command from anywhere. Destination can be anywhere on $PATH.

	chmod +x drush
	sudo mv drush /usr/local/bin
	
Optional. Enrich the bash startup file with completion and aliases.

	drush init

***

*PHP - Drush (c) by Jack Szwergold; written on December 15, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
