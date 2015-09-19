# Cheat Sheet - NETATALK

By Jack Szwergold, September 19, 2015

#### Installing `netatalk`.

Install `netatalk` via `aptitude` like this:

    sudo aptitude install netatalk

#### Start, stop and control `netatalk`.

	sudo service netatalk start
	sudo service netatalk stop
	sudo service netatalk restart
	sudo service netatalk force-reload

#### Sundry `netatalk` items.

Edit the `netatalk` config file:

	sudo nano /etc/netatalk/afpd.conf

Add this line to the end of the config file:

	- -tcp -noddp -uamlist uams_dhx.so,uams_dhx2_passwd.so -nosavepassword

Edit the `AppleVolumes` list:

    sudo nano /etc/netatalk/AppleVolumes.default

Add this line to the end of the config file to define a mount point:

	#/var/www  WebRoot allow:@www-readwrite options:usedots,upriv
	/var/www  "WebRoot (Netatalk)" allow:@www-readwrite options:usedots,upriv,noadouble

***

*Cheat Sheet - NETATALK (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
