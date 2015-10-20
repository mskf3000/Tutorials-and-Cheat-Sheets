# Cheat Sheet - Fail2Ban

By Jack Szwergold, October 19, 2015

#### Install and configure Fail2Ban.

First, install Fail2Ban via `aptitude` like this:

    sudo aptitude install fail2ban

Then copy the default `jail.conf` file to a `jail.local`

    sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

And make all your configuration edits/tweaks to that `jail.local` like this:

    sudo nano /etc/fail2ban/jail.local

Add the IP addresses to be ignored like this; the default is simply `localhost` (`127.0.0.1`):

    ignoreip = 127.0.0.1/8

Let’s fatten that list up a bit by adding these other known local address ranges into play:

    ignoreip = 127.0.0.0/8 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16

The SSH config for Fail2Ban is enabled by default and it’s config looks like this:

	[ssh]
	
	enabled  = true
	port     = ssh
	filter   = sshd
	logpath  = /var/log/auth.log
	maxretry = 6

If you need to enable the HTTP/HTTPS config for Apache, just set this config to `enabled  = true`:

	[apache]
	
	enabled  = true
	port     = http,https
	filter   = apache-auth
	logpath  = /var/log/apache*/*error.log
	maxretry = 6

#### Monitor Fail2Ban.

You can follow the logs here:

    tail -f -n 200 /var/log/fail2ban.log

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
