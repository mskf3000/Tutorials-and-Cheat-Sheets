# Cheat Sheet - ProFTPD

By Jack Szwergold, October 7, 2015

First things first: Unless you are in 1993, there is no reason to use straight FTP anymore. Pretty much every “FTP” connection anyone uses nowadays is SFTP anyway. But on the off chance you have to work on a setup that requires FTP, you could do worse than use ProFTP to manage the service.

And if you restrict FTP access to a specific IP address, it will make the whole idea you are using flawed technology in a modern era easier to swallow; see the section on allowing FTP ports access for more details.

***

Install the basics for the build:

	sudo aptitude install libncurses5-dev libssl0.9.8

Install the Apache ProFTPD module: If asked how to install it, be sure to choose the “standalone” method:

    sudo aptitude install proftpd

#### Squelch `mod_tls_memcache.c` warnings.

But wait! We’re not done with this yet. If you are using Ubuntu 12.04 you might see error warnings like this when the service starts or restarts:

	sandbox proftpd[8412]: mod_tls/2.4.3: compiled using OpenSSL version 'OpenSSL 1.0.0e 6 Sep 2011' headers, but linked to OpenSSL version 'OpenSSL 1.0.1 14 Mar 2012' library
	sandbox proftpd[8412]: mod_sftp/0.9.8: compiled using OpenSSL version 'OpenSSL 1.0.0e 6 Sep 2011' headers, but linked to OpenSSL version 'OpenSSL 1.0.1 14 Mar 2012' library
	sandbox proftpd[8412]: mod_tls_memcache/0.1: notice: unable to register 'memcache' SSL session cache: Memcache support not enabled

The OpenSSL header mismatch stuff is not much to worry about, and the `mod_tls_memcache` can be similarly ignored. But you can at least squealch that specific `mod_tls_memcache` message like this. Open up the `modules.conf` like this:

    sudo nano /etc/proftpd/modules.conf

Find the line that reads like this:

    LoadModule mod_tls_memcache.c

And comment it out like this:

    # LoadModule mod_tls_memcache.c

Now restart ProFTPD like this:

    sudo service proftpd restart

While those OpenSSL messages will still pop up, that `mod_tls_memcache` message should be gone.

#### Allowing FTP ports network access.

If you are using a firewall such as IPTables, make sure it’s set to allow connections on port `21` as well as ports `50000` to `60000` for passive ports:

	-A INPUT -p tcp -m tcp --dport 21 -j ACCEPT
	-A INPUT -p tcp -m tcp --dport 50000:60000 -j ACCEPT

While the above IPTables ruleset would open up FTP access to anyone who can reach your system, if you need to restrict FTP access to a specific IP address, do something like this:

	-A INPUT -s 123.456.789.0/32 -p tcp -m tcp --dport 21 -j ACCEPT
	-A INPUT -s 123.456.789.0/32 -p tcp -m tcp --dport 50000:60000 -j ACCEPT

Remember: FTP protocol is highly insecure nowadays—to say the least—so restricting FTP access to one IP address is the best compromise if you need to use straight, insecure FTP for some reason.

#### Sundry ProFTPD items on an Ubuntu/Debian system.

Get ProFTPD version number:

    proftpd -v

In this case the output should be something like this:

    ProFTPD Version 1.3.4a

Start, stop and control Apache on an Ubuntu/Debian system:

	sudo service proftpd start
	sudo service proftpd status
	sudo service proftpd force-start
	sudo service proftpd stop
	sudo service proftpd force-stop
	sudo service proftpd reload
	sudo service proftpd restart
	sudo service proftpd force-reload
	sudo service proftpd check-config

Open up the ProFTPD config for editing:

    sudo nano /etc/proftpd/proftpd.conf

Follow the ProFTPD log:

	sudo tail -f -n 200 /var/log/proftpd/proftpd.log

#### Setting up SFTP in ProFTPD

The location of the ProFTPD SFTP modules.

	/usr/lib/proftpd/mod_sftp_pam.so
	/usr/lib/proftpd/mod_sftp.so

An example ProFTPD SFTO configuration:

	<IfModule mod_sftp.c>
	
	  SFTPEngine on
	  SFTPLog /var/log/proftpd/sftp.log
	  TransferLog /var/log/proftpd/xferlog-sftp.log
	
	  # Configure the server to listen on the normal SSH2 port, port 22
	  Port 22
	
	  # Configure both the RSA and DSA host keys, using the same host key
	  # files that OpenSSH uses.
	  SFTPHostKey /etc/ssh/ssh_host_rsa_key
	  SFTPHostKey /etc/ssh/ssh_host_dsa_key
	
	  # Configure the file used for comparing authorized public keys of users.
	  SFTPAuthorizedUserKeys file:~/.sftp/authorized_keys
	
	  # Enable compression
	  SFTPCompression delayed
	
	  # Allow the same number of authentication attempts as OpenSSH.
	  #
	  # It is recommended that you explicitly configure MaxLoginAttempts
	  # for your SSH2/SFTP instance to be higher than the normal
	  #   MaxLoginAttempts value for FTP, as there are more ways to authenticate
	  # using SSH2.
	  MaxLoginAttempts 3
	
	</IfModule>

#### ProFTPD: Config with tweaks.

This is an example config that would be placed in this file:

    sudo nano /etc/proftpd/proftpd.conf

The whole file is reproduced below, but the specific items changed in this example are:

- `DefaultRoot`: Set to `/var/www/` instead of the “user jail” of `~`.
- `PassivePorts`: Set to range from `50000` to `60000` instead of `49152` to `65534`.
- `Umask`: Set to `002` and `002` instead of `022` and `022`.

And here is the actual, full ProFTPD config file for review:

	#
	# /etc/proftpd/proftpd.conf -- This is a basic ProFTPD configuration file.
	# To really apply changes, reload proftpd after modifications, if
	# it runs in daemon mode. It is not required in inetd/xinetd mode.
	#
	
	# Includes DSO modules
	Include /etc/proftpd/modules.conf
	
	# Set off to disable IPv6 support which is annoying on IPv4 only boxes.
	UseIPv6				on
	# If set on you can experience a longer connection delay in many cases.
	IdentLookups			off
	
	ServerName			"Debian"
	ServerType			standalone
	DeferWelcome			off
	
	MultilineRFC2228		on
	DefaultServer			on
	ShowSymlinks			on
	
	TimeoutNoTransfer		600
	TimeoutStalled			600
	TimeoutIdle			1200
	
	DisplayLogin                    welcome.msg
	DisplayChdir               	.message true
	ListOptions                	"-l"
	
	DenyFilter			\*.*/
	
	# Use this to jail all users in their homes
	# DefaultRoot			~
	DefaultRoot                     /var/www/
	
	# Users require a valid shell listed in /etc/shells to login.
	# Use this directive to release that constrain.
	# RequireValidShell		off
	
	# Port 21 is the standard FTP port.
	Port				21
	
	# In some cases you have to specify passive ports range to by-pass
	# firewall limitations. Ephemeral ports can be used for that, but
	# feel free to use a more narrow range.
	# PassivePorts                  49152 65534
	PassivePorts                  50000 60000
	
	# If your host was NATted, this option is useful in order to
	# allow passive tranfers to work. You have to use your public
	# address and opening the passive ports used on your firewall as well.
	# MasqueradeAddress		1.2.3.4
	
	# This is useful for masquerading address with dynamic IPs:
	# refresh any configured MasqueradeAddress directives every 8 hours
	<IfModule mod_dynmasq.c>
	# DynMasqRefresh 28800
	</IfModule>
	
	# To prevent DoS attacks, set the maximum number of child processes
	# to 30.  If you need to allow more than 30 concurrent connections
	# at once, simply increase this value.  Note that this ONLY works
	# in standalone mode, in inetd mode you should use an inetd server
	# that allows you to limit maximum number of processes per service
	# (such as xinetd)
	MaxInstances			30
	
	# Set the user and group that the server normally runs at.
	User				proftpd
	Group				nogroup
	
	# Umask 022 is a good standard umask to prevent new files and dirs
	# (second parm) from being group and world writable.
	# Umask				022  022
	Umask				002  002
	
	# Normally, we want files to be overwriteable.
	AllowOverwrite			on
	
	# Uncomment this if you are using NIS or LDAP via NSS to retrieve passwords:
	# PersistentPasswd		off
	
	# This is required to use both PAM-based authentication and local passwords
	# AuthOrder			mod_auth_pam.c* mod_auth_unix.c
	
	# Be warned: use of this directive impacts CPU average load!
	# Uncomment this if you like to see progress and transfer rate with ftpwho
	# in downloads. That is not needed for uploads rates.
	#
	# UseSendFile			off
	
	TransferLog /var/log/proftpd/xferlog
	SystemLog   /var/log/proftpd/proftpd.log
	
	# In order to keep log file dates consistent after chroot, use timezone info
	# from /etc/localtime.  If this is not set, and proftpd is configured to
	# chroot (e.g. DefaultRoot or <Anonymous>), it will use the non-daylight
	# savings timezone regardless of whether DST is in effect.
	#SetEnv TZ :/etc/localtime
	
	<IfModule mod_quotatab.c>
	QuotaEngine off
	</IfModule>
	
	<IfModule mod_ratio.c>
	Ratios off
	</IfModule>
	
	
	# Delay engine reduces impact of the so-called Timing Attack described in
	# http://security.lss.hr/index.php?page=details&ID=LSS-2004-10-02
	# It is on by default.
	<IfModule mod_delay.c>
	DelayEngine on
	</IfModule>
	
	<IfModule mod_ctrls.c>
	ControlsEngine        off
	ControlsMaxClients    2
	ControlsLog           /var/log/proftpd/controls.log
	ControlsInterval      5
	ControlsSocket        /var/run/proftpd/proftpd.sock
	</IfModule>
	
	<IfModule mod_ctrls_admin.c>
	AdminControlsEngine off
	</IfModule>
	
	#
	# Alternative authentication frameworks
	#
	#Include /etc/proftpd/ldap.conf
	#Include /etc/proftpd/sql.conf
	
	#
	# This is used for FTPS connections
	#
	#Include /etc/proftpd/tls.conf
	
	#
	# Useful to keep VirtualHost/VirtualRoot directives separated
	#
	#Include /etc/proftpd/virtuals.con
	
	# A basic anonymous configuration, no upload directories.
	
	# <Anonymous ~ftp>
	#   User				ftp
	#   Group				nogroup
	#   # We want clients to be able to login with "anonymous" as well as "ftp"
	#   UserAlias			anonymous ftp
	#   # Cosmetic changes, all files belongs to ftp user
	#   DirFakeUser	on ftp
	#   DirFakeGroup on ftp
	#
	#   RequireValidShell		off
	#
	#   # Limit the maximum number of anonymous logins
	#   MaxClients			10
	#
	#   # We want 'welcome.msg' displayed at login, and '.message' displayed
	#   # in each newly chdired directory.
	#   DisplayLogin			welcome.msg
	#   DisplayChdir		.message
	#
	#   # Limit WRITE everywhere in the anonymous chroot
	#   <Directory *>
	#     <Limit WRITE>
	#       DenyAll
	#     </Limit>
	#   </Directory>
	#
	#   # Uncomment this if you're brave.
	#   # <Directory incoming>
	#   #   # Umask 022 is a good standard umask to prevent new files and dirs
	#   #   # (second parm) from being group and world writable.
	#   #   Umask				022  022
	#   #            <Limit READ WRITE>
	#   #            DenyAll
	#   #            </Limit>
	#   #            <Limit STOR>
	#   #            AllowAll
	#   #            </Limit>
	#   # </Directory>
	#
	# </Anonymous>
	
	# Include other custom configuration files
	Include /etc/proftpd/conf.d/

***

*Cheat Sheet - ProFTPD (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
