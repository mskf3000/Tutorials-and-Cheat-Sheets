# Fail2Ban

By Jack Szwergold, October 19, 2015

### Install Fail2Ban.

First, install Fail2Ban via `aptitude` like this; this will install verison 0.8.6 of Fail2Ban on Ubuntu 12.04:

    sudo aptitude install fail2ban

But like I said, it installs Fail2Ban version 0.8.6. The problem with that? Beginning with version 0.8.7 a `recidive` filter was introduced that allows you to extend a banning period for persistent abusers. Very useful tool. So the best way to get that functionality is to install the 0.8.13 `.deb` package manually like this.

First purge the older 0.8.6 version of Fail2Ban that was installed like this:

    sudo aptitude purge fail2ban

With that done, let’s manually install the dependencies manually like this:

    sudo aptitude install gamin libgamin0 python-central python-gamin python-support

Then grab the raw `.deb` for Fail2Ban 0.8.13 like this:

    curl -O -L http://old-releases.ubuntu.com/ubuntu/pool/universe/f/fail2ban/fail2ban_0.8.13-1_all.deb

And then install it like this:

    sudo dpkg -i fail2ban_0.8.13-1_all.deb

Once that is done, check the version of Fail2Ban like this:

    fail2ban-client --version

Returned output should be something like this and you should be good to go:

	Fail2Ban v0.8.13
	
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

Get the list of jails Fail2Ban is using:

    sudo fail2ban-client status

Get the status of a specific jail like this:

    sudo fail2ban-client status ssh

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

To get Fail2Ban to send out emails, adjust the `action` settion to be ban only (`action_`), ban and send an email with WHOIS info (`action_mw`) or ban, send an email with WHOIS info as well as log lines (`action_mwl`):

	# action = %(action_)s
	action = %(action_mwl)s

#### Configuring Fail2Ban jails.

The SSH jail config for Fail2Ban is enabled by default and it’s config looks like this:

	[ssh]
	
	enabled  = true
	port     = ssh
	filter   = sshd
	logpath  = /var/log/auth.log
	maxretry = 6

I prefer to set it to this so the `bantime` is 5 minutes (300 seconds), the `findtime` to catch attempts is 15 minutes (900 seconds) and the `maxretry` is 3:

	[ssh]
	
	enabled  = true
	port     = ssh
	filter   = sshd
	logpath  = /var/log/auth.log
	bantime  = 180
	findtime = 1800
	maxretry = 3

Similarly we can enable and adjust the SSH DDoS detection check jail like this; `bantime` is 5 minutes (300 seconds), the `findtime` to catch attempts is 15 minutes (900 seconds) and the `maxretry` is 3:

	[ssh-ddos]
	
	enabled  = true
	port     = ssh
	filter   = sshd-ddos
	logpath  = /var/log/auth.log
	bantime  = 180
	findtime = 1800
	maxretry = 3

Similarly we can enable and adjust the Apache basic authentication failure check jail like this; `bantime` is 5 minutes (300 seconds), the `findtime` to catch attempts is 15 minutes (900 seconds) and the `maxretry` is 3:

	[apache]
	
	enabled  = true
	port     = http,https
	filter   = apache-auth
	logpath  = /var/log/apache*/*error.log
	bantime  = 180
	findtime = 1800
	maxretry = 3

Similarly we can enable and adjust the Apache overflows failure check jail like this; `bantime` is 5 minutes (300 seconds) and the `findtime` to catch attempts is 15 minutes (900 seconds):

	[apache-overflows]
	
	enabled  = true
	port     = http,https
	filter   = apache-overflows
	logpath  = /var/log/apache*/*error.log
	bantime  = 180
	findtime = 1800
	maxretry = 2

And if you are using Fail2Ban verison 0.8.7 or higher, be sure to activate the `recidive` filter so repeat offenders are banned for a nice long time. This is adjusted so `maxretry` is 3 instead of 5 and there is a destination (`dest=`) email set to notify me of `recidive` blocking someone:

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
	           sendmail-whois-lines[name=recidive, dest=email_address@example.com, logpath=/var/log/fail2ban.log]
	bantime  = 604800  ; 1 week
	findtime = 86400   ; 1 day
	maxretry = 3

Then restart the Fail2Ban service like this:

    sudo service fail2ban restart

#### Adding a custom Apache “Bad Bots” jail.

Open up `jail.local` like this:

    sudo nano /etc/fail2ban/jail.local

And add this new `apache-badbots` jail to the bottom of the file:

	# Adding a custom 'apache-badbots' jail.
	[apache-badbots]
	
	enabled  = true
	port     = http,https
	filter   = apache-badbots
	logpath  = /var/log/apache*/*access.log
	bantime  = 180
	findtime = 1800
	maxretry = 2

If you need to customize the bots or the regex that scans the logs for bots, just open up the `apache-badbots` config file:

    sudo nano /etc/fail2ban/filter.d/apache-badbots.conf

And edit items as you see fit. In my case, I want to add `Baiduspider` to the list like this:

	# badbotscustom = EmailCollector|WebEMailExtrac|TrackBack/1\.02|sogou music spider
	badbotscustom = EmailCollector|WebEMailExtrac|TrackBack/1\.02|sogou music spider|Baiduspider

Then just restart Fail2Ban and that jail should be all set.

#### Adding a custom DDoS jail.

This setup hinges on your core IPTables rules being set to not only catch DDoS attempts on a packet level but also logging the output to the Kernel log (`kern.log`) with a prefix of `IPTABLES_DENIED`. If you have that setup, read on. And if you log DDoS attempts with a prefix other than `IPTABLES_DENIED` feel free to adjust it.

***

First, create the `ddos.conf`:

    sudo nano /etc/fail2ban/filter.d/ddos.conf

And add this custom DDoS definition to the bottom of the config; the core of this definition is the `failregex`:

    [Definition]

    # Option:  failregex
    # Notes.:  Auto block short UDP.
    # Values:  TEXT
    #
    # failregex = .*(?:IPTABLES_DENIED_TCP).*SRC=<HOST>.*
    # failregex = .*(?:IPTABLES_DENIED_UDP).*SRC=<HOST>.*
    # failregex = .*(?:IPTABLES_DENIED_ICMP).*SRC=<HOST>.*
    failregex = .*(?:IPTABLES_DENIED).*SRC=<HOST>.*

    ignoreregex =

You can test that DDoS definition by running this command:

    sudo fail2ban-regex -v /var/log/kern.log /etc/fail2ban/filter.d/ddos.conf

If all looks good, then add a DDoS jail to your setup, by editing the `jail.local`:

    sudo nano /etc/fail2ban/jail.local

And add this custom jail to the bottom of the config:

    # Adding a custom 'ddos' jail.
    [ddos]

    enabled   = true
    banaction = iptables-allports
    port      = anyport
    filter    = ddos
    logpath   = /var/log/kern.log
    bantime   = 180
    findtime  = 900
    maxretry  = 3

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

	logSys.warn("[%s] Ban %s %s" % (self.jail.getName(), aInfo["ip"], commands.getstatusoutput('geoiplookup ' + aInfo["ip"])[1][23:]))

### Monitor Fail2Ban.

The socket file can be found here:

    /var/run/fail2ban/fail2ban.sock

The process ID file can be found here:

    /var/run/fail2ban/fail2ban.pid

#### Follow the logs.

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

If you feel like checking the main `auth.log` for failed login attempts, just run this command:

    sudo tail -f -n 500 /var/log/auth.log | grep "Failed password"

And you can even get a nice tally of failed password attempts by running this command that parses through compressed and uncompressed archives:

    sudo zgrep -h 'Failed password' /var/log/auth.* | grep sshd | awk '{print $1,$2}' | sort -k 1,1M -k 2n | uniq -c

The output would be something like this:

	   10 /var/log/auth.log.1:Nov 1
	 9762 /var/log/auth.log.4.gz:Oct 4
	15350 /var/log/auth.log.4.gz:Oct 5
	12358 /var/log/auth.log.4.gz:Oct 6
	12692 /var/log/auth.log.4.gz:Oct 7
	 8377 /var/log/auth.log.4.gz:Oct 8
	10875 /var/log/auth.log.4.gz:Oct 9
	  565 /var/log/auth.log.4.gz:Oct 10
	12518 /var/log/auth.log.3.gz:Oct 11
	 3509 /var/log/auth.log.4.gz:Oct 11
	10422 /var/log/auth.log.3.gz:Oct 12
	 6808 /var/log/auth.log.3.gz:Oct 13
	26891 /var/log/auth.log.3.gz:Oct 14
	 9493 /var/log/auth.log.3.gz:Oct 15
	 5138 /var/log/auth.log.3.gz:Oct 16
	 9415 /var/log/auth.log.3.gz:Oct 17
	 5909 /var/log/auth.log.2.gz:Oct 18
	 2226 /var/log/auth.log.3.gz:Oct 18
	13444 /var/log/auth.log.2.gz:Oct 19
	  351 /var/log/auth.log.2.gz:Oct 20
	  162 /var/log/auth.log.2.gz:Oct 21
	  499 /var/log/auth.log.2.gz:Oct 22
	  377 /var/log/auth.log.2.gz:Oct 23
	  145 /var/log/auth.log.2.gz:Oct 24
	 8275 /var/log/auth.log.1:Oct 25
	 2622 /var/log/auth.log.2.gz:Oct 25
	   76 /var/log/auth.log.1:Oct 26
	   54 /var/log/auth.log.1:Oct 27
	  310 /var/log/auth.log.1:Oct 28
	 1024 /var/log/auth.log.1:Oct 29
	  208 /var/log/auth.log.1:Oct 30
	   39 /var/log/auth.log.1:Oct 31

#### Filter out a list of IP addresses.

To get a nice list of IPs that have been baned and for what reason just run this Grep command:

    sudo grep "Ban " /var/log/fail2ban.log | awk -F[\ \:] '{print $10,$8}'

That would spit out a list of IPs and the jail they were snagged in like this:

	43.229.53.86 [ssh]
	115.85.192.40 [ssh]
	43.229.53.86 [ssh]
	43.229.53.86 [ssh]
	43.229.53.86 [recidive]
	43.229.53.86 [recidive]
	43.229.53.86 [ssh]
	43.229.53.86 [recidive]
	43.229.53.86 [ssh]
	43.229.53.86 [recidive]
	43.229.53.86 [ssh]

But if you just need a list of IP addresses, run this command:

    sudo grep "Ban " /var/log/fail2ban.log | awk -F[\ \:] '{print $10}'

And the output would be something like this:

	43.229.53.86
	115.85.192.40
	43.229.53.86
	43.229.53.86
	43.229.53.86
	43.229.53.86
	43.229.53.86
	43.229.53.86
	43.229.53.86
	43.229.53.86
	43.229.53.86

And you can even cleanup the Fail2Ban log output so it’s a bit cleaner to parse with an Awk command like this:

    sudo cat /var/log/fail2ban.log | awk '/WARNING/ && /Ban/ { split($2,split_2,","); printf "%s %s %s %s %s\n", $1, split_2[1], substr($5, 2, length($5) - 2), $6, $7 }'

And the output would be something like this:

	2015-10-26 00:34:08 ssh Ban 43.229.53.86
	2015-10-26 00:36:01 ssh Ban 43.229.53.86
	2015-10-26 01:08:18 ssh Ban 43.229.53.86
	2015-10-26 01:08:20 recidive Ban 43.229.53.86
	2015-10-26 02:23:31 ssh Ban 119.252.171.158
	2015-10-26 03:21:43 ssh Ban 119.252.171.158
	2015-10-26 03:40:22 ssh Ban 119.252.171.158
	2015-10-26 03:40:24 recidive Ban 119.252.171.158
	2015-10-26 03:55:30 apache Ban 222.186.21.181
	2015-10-26 08:33:41 ssh Ban 193.107.16.206
	2015-10-26 11:44:14 ssh Ban 66.87.117.94
	2015-10-26 14:17:30 ssh Ban 113.98.255.48
	2015-10-27 01:22:37 recidive Ban 119.252.171.158
	2015-10-27 01:46:52 ssh Ban 43.229.53.86
	2015-10-27 02:21:20 ssh Ban 43.229.53.86
	2015-10-27 02:22:30 ssh Ban 169.55.111.212
	2015-10-27 03:04:30 ssh Ban 43.229.53.86
	2015-10-27 03:04:32 recidive Ban 43.229.53.86
	2015-10-27 05:09:02 ssh Ban 119.167.153.189
	2015-10-27 13:04:40 ssh-ddos Ban 190.220.129.66
	2015-10-27 17:01:12 ssh Ban 197.157.244.243

***

*Fail2Ban (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
