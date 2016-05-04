## Mac OS X - Uninstalling MySQL from Mac OS X

By Jack Szwergold, September 14, 2015

#### Uninstalling MySQL from Mac OS X.

If you installed MySQL from an official package installer and you want to now uninstall it, just run the following commands. These commands assume the version of MySQL that was installed was `mysql-5.5.12-osx10.6-x86_64` so adjust accordingly to match your setup:

First, go into `/usr/local/`:

	cd /usr/local/

And get rid of the core MySQL stuff like this:
	
	sudo rm mysql	
	sudo rm -rf mysql-5.5.12-osx10.6-x86_64

Next, go into `/Library/StartupItems/`:

	cd /Library/StartupItems/
	
And get rid of the `MySQLCOM` startup item like this:

	sudo rm -rf MySQLCOM
	
Then, go into `/Library/PreferencePanes/`:

	cd /Library/PreferencePanes/
	
And get rid of the `MySQL.prefPane` preference pane like this:

	sudo rm -rf MySQL.prefPane

Finally, head into `/Library/Receipts/`:

	cd /Library/Receipts/

And get rid of all MySQL related installation receipts like this:

	sudo rm -rf mysql*	
	sudo rm -rf MySQL*

Similarly, head into `/var/db/receipts/`:

	cd /var/db/receipts/

And get rid of all MySQL related installation receipts like this:

	sudo rm com.mysql.mysql.bom
	sudo rm com.mysql.mysql.plist
	sudo rm com.mysql.mysqlstartup.bom
	sudo rm com.mysql.mysqlstartup.plist

Now edit `/etc/hostconfig`:

    sudo nano /etc/hostconfig

And remove the line MySQL related line that should look like this:

    MYSQLCOM=-YES-

***

<sup>*Mac OS X - Uninstalling MySQL from Mac OS X (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>