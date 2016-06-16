## Ubuntu - Locale Related Items

By Jack Szwergold

### Fixing Perl local errors on Ubuntu.

If you get errors like this running Perl on an Ubuntu server:

	perl: warning: Setting locale failed.
	perl: warning: Please check that your locale settings:
		LANGUAGE = "en_US:en",
		LC_ALL = (unset),
		LANG = "en_US.UTF-8"
	    are supported and installed on your system.
	perl: warning: Falling back to the standard locale ("C").

Run the following `export` commands:

	export LANGUAGE=en_US.UTF-8
	export LANG=en_US.UTF-8
	export LC_ALL=en_US.UTF-8

Then run `locale-gen` like this:

	sudo locale-gen en_US.UTF-8

Then run `dpkg-reconfigure locales` like this:

	sudo dpkg-reconfigure locales

Reboot your machine and it should all be cleared when it comes back online:

	sudo shutdown -r now

Check the output of `locale` which should now be something like this:

	LANG=en_US.UTF-8
	LANGUAGE=
	LC_CTYPE="en_US.UTF-8"
	LC_NUMERIC="en_US.UTF-8"
	LC_TIME="en_US.UTF-8"
	LC_COLLATE="en_US.UTF-8"
	LC_MONETARY="en_US.UTF-8"
	LC_MESSAGES="en_US.UTF-8"
	LC_PAPER="en_US.UTF-8"
	LC_NAME="en_US.UTF-8"
	LC_ADDRESS="en_US.UTF-8"
	LC_TELEPHONE="en_US.UTF-8"
	LC_MEASUREMENT="en_US.UTF-8"
	LC_IDENTIFICATION="en_US.UTF-8"
	LC_ALL=

***

*Ubuntu - Locale Related Items (c) by Jack Szwergold; written on October 5, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*