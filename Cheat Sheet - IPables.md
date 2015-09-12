# Cheat Sheet - IPables

By Jack Szwergold, September 11, 2015

#### Start, stop and control `iptables`

On Ubuntu/Debian systems you can start, stop, restart and flush `iptables` this way:

	sudo service iptables-persistent start
	sudo service iptables-persistent stop
	sudo service iptables-persistent restart
	sudo service iptables-persistent flush

On CentOS/RedHat systems you can start, stop, restart and flush `iptables` this way:

    sudo /etc/rc.d/init.d/iptables start
    sudo /etc/rc.d/init.d/iptables stop
    sudo /etc/rc.d/init.d/iptables restart
    sudo /etc/rc.d/init.d/iptables flush

#### Different ways to showing `iptables` rules.

List all rules (`-L`) in the selected table—default is `-t filter`—with hostname lookups.

	sudo iptables -L

List all rules (`-L`) in the selected table—default is `-t filter`—in numeric format (`-n`) without hostname lookups.

	sudo iptables -L -n

List all rules (`-L`) in the selected table—default is `-t filter`—in numeric format (`-n`) without hostname lookups and add line numbers (`--line-numbers`) to each line.

	sudo iptables --line-numbers -n -L

List all rules (`-L`) in the NAT table (`-t nat`) in numeric format (`-n`) without hostname lookups.

    sudo iptables -t nat -L -n

#### Install and setup `iptables-persistent`.

By default, an Ubuntu/Debian systems lose `iptables` rules on reboot. Installing `iptables-persistent` assures that rules are saved and reloaded on reboot:

    sudo aptitude install iptables-persistent

If `iptables` rules have already been set on the system, export the rules into a text file like this:

    sudo iptables-save > ~/iptables.conf

Now create an `iptables-persistent` specific storage directory if it doesn’t exist already:

    sudo mkdir /etc/iptables

On Ubuntu/Debian you can copy the rules saved in the text file `~/iptables.conf` into the `/etc/iptables/rules.v4` location so `iptables-persistent` can load them:

    sudo cp ~/iptables.conf /etc/iptables/rules.v4

On RedHat/CentOS you can copy the rules saved in the text file `~/iptables.conf` into the `/etc/iptables/rules.v4` location so `iptables-persistent` can load them:

    sudo cp ~/iptables.conf /etc/sysconfig/iptables

And with that done, `iptables-persistent` should be all set.

#### Manually importing `iptables` rules.

To manually import `iptables` rules, just use `iptables-restore` like this:

    sudo iptables-restore < ~/iptables.conf

#### Flushing `iptables` rules.

To dump all `iptables` rules, use the `-F` flag to flush all of the `iptables` rules in all chains:

    sudo iptables -F

#### Delete an `iptables` chain.

To delete all non-core `iptables` chains, just use the `-X` flag like so:

    sudo iptables -X

To delete a specific `iptables` chain, just use the `-X` flag like so and be sure to indicate the name of the chain to be deleted:

    sudo iptables -X [chain name]

### A simple `PREROUTING` example.

To set port rerouting directly in the command line, run a command like this; this command assumes the source IP of `11.22.33.44` will connect on port `8000` and get traffic from port `80`:

    sudo iptables -A PREROUTING -t nat -s 11.22.33.44 --p tcp --dport 8000 -j REDIRECT --to-ports 80

Or you can add this rule to the `*nat` table into the `iptables.conf` text file; this command assumes the source IP of `11.22.33.44` will connect on port `8000` and get traffic from port `80`:

    -A PREROUTING -s 11.22.33.44 -p tcp -m tcp --dport 8000 -j REDIRECT --to-ports 80

***

*Cheat Sheet - IPables (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*