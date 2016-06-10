## Mac OS X - MacPorts

By Jack Szwergold, September 22, 2015

This tutorial explains how you can uninstall MacPorts on Mac OS X.

List the contents of the `/opt/local` directory used by MacPorts:

	ls -la /opt/local/

Get the size of the `/opt/local` directory used by MacPorts:

    sudo du -sh /opt/local/

Clean out old MacPorts stuff.

	sudo port clean --all installed
	
	sudo port -f uninstall inactive

Uninstall MacPorts:

	sudo port -fp uninstall installed

Check the MacPorts stuff on the system:

	ls -la /opt/local
	ls -la /Applications/DarwinPorts
	ls -la /Applications/MacPorts
	ls -la /Library/LaunchDaemons/org.macports.*
	ls -la /Library/Receipts/DarwinPorts*.pkg
	ls -la /Library/Receipts/MacPorts*.pkg
	ls -la /Library/StartupItems/DarwinPortsStartup
	ls -la /Library/Tcl/darwinports1.0
	ls -la /Library/Tcl/macports1.0
	rm ~/.macports

Remove the the MacPorts stuff on the system:

	sudo rm -rf \
	        /opt/local \
	        /Applications/DarwinPorts \
	        /Applications/MacPorts \
	        /Library/LaunchDaemons/org.macports.* \
	        /Library/Receipts/DarwinPorts*.pkg \
	        /Library/Receipts/MacPorts*.pkg \
	        /Library/StartupItems/DarwinPortsStartup \
	        /Library/Tcl/darwinports1.0 \
	        /Library/Tcl/macports1.0 \
	        ~/.macports

Remove the `.plist` user and group files for the `macports` user (and related group) that was created when MacPorts was intially installed:

    sudo rm -f /private/var/db/dslocal/nodes/Default/users/macports.plist
    sudo rm -f /private/var/db/dslocal/nodes/Default/groups/macports.plist

***

*Mac OS X - MacPorts (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*