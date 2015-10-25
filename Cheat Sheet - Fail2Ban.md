# Cheat Sheet - Fail2Ban

By Jack Szwergold, October 19, 2015

### Install Fail2Ban.

First, install Fail2Ban via `aptitude` like this; this will install verison 0.8.6 of Fail2Ban on Ubuntu 12.04:

    sudo aptitude install fail2ban

But like I said, it installs Fail2Ban version 0.8.6. The problem with that? Beginning with version 0.8.7 a `recidive` filter was introduced that allows you to extend a banning period for persistent abusers. Very useful tool. So the best way to get that functionality is to install the 0.8.11 `.deb` package manually like this.

First purge the older 0.8.6 version of Fail2Ban that was installed like this:

    sudo aptitude purge fail2ban

With that done, let’s manually install the dependencies manually like this:

    sudo aptitude install gamin libgamin0 python-central python-gamin python-support

Then grab the raw `.deb` for Fail2Ban 0.8.11 like this:

    curl -O -L http://mirrors.kernel.org/ubuntu/pool/universe/f/fail2ban/fail2ban_0.8.11-1_all.deb
    
Or even grab the raw `.deb` for Fail2Ban 0.8.13 like this:

    curl -O -L http://old-releases.ubuntu.com/ubuntu/pool/universe/f/fail2ban/fail2ban_0.8.13-1_all.deb

And then install it like this:

    sudo dpkg -i fail2ban_0.8.11-1_all.deb

Or like this for Fail2Ban 0.8.13:

    sudo dpkg -i fail2ban_0.8.13-1_all.deb

Once that is done, check the version of Fail2Ban like this:

    fail2ban-client --version

Returned output should be something like this and you should be good to go:

	Fail2Ban v0.8.11
	
	Copyright (c) 2004-2008 Cyril Jaquier, 2008- Fail2Ban Contributors
	Copyright of modifications held by their respective authors.
	Licensed under the GNU General Public License v2 (GPL).
	
	Written by Cyril Jaquier <cyril.jaquier@fail2ban.org>.
	Many contributions by Yaroslav O. Halchenko <debian@onerussian.com>.

If you somehow need to uninstall this version `dpkg` install of Fail2Ban, just do the following. First find out the package name like this:

    dpkg -l | grep fail2ban

Knowing that just uninstall it like this:

    sudo dpkg -r fail2ban

### Sundry Apache items on an Ubuntu/Debian system.

Get the Fail2Ban version number:

	fail2ban-client --version

Start, stop and control Fail2Ban on an Ubuntu/Debian system:

	sudo service fail2ban restart
	sudo service fail2ban start
	sudo service fail2ban stop

### Configure Fail2Ban.

Once it’s installed, copy the default `jail.conf` file to a `jail.local`

    sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

And make all your configuration edits/tweaks to that `jail.local` like this:

    sudo nano /etc/fail2ban/jail.local

Add the IP addresses to be ignored like this; the default is simply `localhost` (`127.0.0.1`):

    ignoreip = 127.0.0.1/8

Let’s fatten that list up a bit by adding these other known local address ranges into play:

    # ignoreip = 127.0.0.1/8
    ignoreip = 127.0.0.0/8 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16

Also, the default `bantime` value is set to 10 minutes (aka: 600 seconds). I like to set it to 300 if I have `recidive` set. But feel free to adjust if need be:

	# bantime  = 600
	bantime  = 300

The SSH config for Fail2Ban is enabled by default and it’s config looks like this:

	[ssh]
	
	enabled  = true
	port     = ssh
	filter   = sshd
	logpath  = /var/log/auth.log
	maxretry = 6

I prefer to set it to this so the `bantime` is 5 minutes (300 seconds) and the `findtime` to catch attempts is 15 minutes (900 seconds):

	[ssh]
	
	enabled  = true
	port     = ssh
	filter   = sshd
	logpath  = /var/log/auth.log
	bantime  = 300
	findtime = 900
	maxretry = 6

And if you are using Fail2Ban verison 0.8.7 or higher, be sure to activate the `recidive` filter so repeat offenders are banned for a nice long time:

	# Jail for more extended banning of persistent abusers
	# !!! WARNING !!!
	#   Make sure that your loglevel specified in fail2ban.conf/.local
	#   is not at DEBUG level -- which might then cause fail2ban to fall into
	#   an infinite loop constantly feeding itself with non-informative lines
	[recidive]
	
	enabled  = true
	filter   = recidive
	logpath  = /var/log/fail2ban.log
	action   = iptables-allports[name=recidive]
	           sendmail-whois-lines[name=recidive, logpath=/var/log/fail2ban.log]
	bantime  = 604800  ; 1 week
	findtime = 86400   ; 1 day
	maxretry = 5

You might want to adjust `recidive` filter so `maxretry` is 3 instead of 5 like this:

	maxretry = 3

Then stop Fail2Ban service like this:

    sudo service fail2ban stop

And start the Fail2Ban service like this:

    sudo service fail2ban start

You would assume that using `restart` would work, but apparently it’s a bit flaky so it’s always best to manually stop and start the service.

#### Dealing with `ignoreregex` warnings.

Also you might see an error message like this when you strat Fail2Ban:

    WARNING 'ignoreregex' not defined in 'Definition'. Using default one: ''

It’s a simple warning, but to squealch it just open up the `postfix-sasl.conf` config like this:

    sudo nano /etc/fail2ban/filter.d/postfix-sasl.conf

And add this line to the bottom to define a `ignoreregex`:

    ignoreregex =

#### Logging GeoIP data.

If you want to add GeoIP lookup data to Fail2Ban logs just do the following. First, make sure you have `geoiplookup` and related databases installed. If you do, then open up, `actions.py` like this:

    sudo nano /usr/share/fail2ban/server/actions.py

And find this line of code:

    logSys.warn("[%s] Ban %s" % (self.jail.getName(), aInfo["ip"]))

And tweak it to be this:

	# logSys.warn("[%s] Ban %s" % (self.jail.getName(), aInfo["ip"]))
	logSys.warn("[%s] Ban %s %s" % (self.jail.getName(), aInfo["ip"], commands.getstatusoutput('geoiplookup ' + aInfo["ip"])[1][23:]))

### Monitor Fail2Ban.

The socket file can be found here:

    /var/run/fail2ban/fail2ban.sock

The process ID file can be found here:

    /var/run/fail2ban/fail2ban.pid

You can follow the logs here:

    sudo tail -f -n 200 /var/log/fail2ban.log

Log output would look something like this:

	2015-10-19 19:22:14,317 fail2ban.jail   : INFO   Creating new jail 'ssh'
	2015-10-19 19:22:14,487 fail2ban.jail   : INFO   Jail 'ssh' uses Gamin
	2015-10-19 19:22:14,843 fail2ban.filter : INFO   Added logfile = /var/log/auth.log
	2015-10-19 19:22:14,844 fail2ban.filter : INFO   Set maxRetry = 6
	2015-10-19 19:22:14,845 fail2ban.filter : INFO   Set findtime = 600
	2015-10-19 19:22:14,845 fail2ban.actions: INFO   Set banTime = 600
	2015-10-19 19:22:14,871 fail2ban.jail   : INFO   Jail 'ssh' started

And since Fail2Ban basically uses IPTables to set rules, you can check the rules Fail2Ban sets by running this command:

    sudo iptables -L -n

The output would be something like this; just pay attention to the `fail2ban` lines the rest of the rules are based on how deep and complex your IPTables rules are:

	Chain INPUT (policy ACCEPT)
	target     prot opt source               destination
	fail2ban-apache  tcp  --  0.0.0.0/0            0.0.0.0/0            multiport dports 80,443
	fail2ban-ssh  tcp  --  0.0.0.0/0            0.0.0.0/0            multiport dports 22
	
	Chain FORWARD (policy ACCEPT)
	target     prot opt source               destination
	
	Chain OUTPUT (policy ACCEPT)
	target     prot opt source               destination
	
	Chain fail2ban-apache (1 references)
	target     prot opt source               destination
	RETURN     all  --  0.0.0.0/0            0.0.0.0/0
	
	Chain fail2ban-ssh (1 references)
	target     prot opt source               destination
	RETURN     all  --  0.0.0.0/0            0.0.0.0/0

***

*Cheat Sheet - Fail2Ban (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
