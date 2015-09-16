# Cheat Sheet - Munin - Moving and Renaming Data Files

By Jack Szwergold, September 16, 2015

This tutorial explains how to move or change a Munin node name without losing past data files and related history.

For this example we are moving data saved under the hostname `localdomain`—default Munin install hostname—to the new hostname of `sandbox.local`.

***

#### Stop the `munin` node.

First step is to stop the Munin node while we perform the work:

    sudo service munin-node stop

#### Adjust the `munin` node config.

Next, open up the main Munin config file on the server:

	sudo nano /etc/munin/munin.conf

And find the host name in the “host tree” and change/adjust it as you need to; in this example we are changing it to `sandbox.local`:

	# a simple host tree
	[sandbox.local]
	    address 127.0.0.1
	    use_node_name yes
	    # contacts serveralert

#### Rename the `munin` node raw datafiles in `/var/lib/munin/`.

Once that is done, save the file and go into the Munin raw data directory in `/var/lib/`:

    cd /var/lib/munin/

Archive the `localdomain` Munin raw data directory with `tar`:

    sudo tar -cf localdomain.tar localdomain

And compress that `tar` archive with `gzip`:

    sudo gzip localdomain.tar

Change the permissions of the files and directories under `localdomain` to whatever your username is; change `username` to match that username so we can rename files and directories:

    sudo chown username:username -R localdomain

Shell script to change the filenames to match the new domain.

	for file in /var/lib/munin/local/*.rrd; do \
		sudo mv $file `echo $file|sed 's/localdomain/sandbox\.local/'`; \
	done

Change the directory name to match the new hostname:

    sudo mv localdomain sandbox.local

And finally, change the permissions of the directory and files back to `munin`:

    sudo chown munin:munin -R sandbox.local

#### Rename the `munin` node web datafiles in `/var/lib/munin/`.

Once that is done, save the file and go into the Munin web data directory in `/var/cache/munin/www/`:

    cd /var/cache/munin/www/

Archive the `localdomain` Munin raw data directory with `tar`:

    sudo tar -cf localdomain.tar localdomain

And compress that `tar` archive with `gzip`:

    sudo gzip localdomain.tar

Change the name of the directories to match the new configs:

    sudo mv localdomain local

#### Start the `munin` node.

And the final step is to restart the Munin node again:

    sudo service munin-node start

***

*Cheat Sheet - Munin - Moving and Renaming Data Files (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*