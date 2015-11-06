# Cheat Sheet - Apache - Mod Evasive

By Jack Szwergold, September 27, 2015

***

Install the Apache `mod_evasive` module:

    sudo aptitude install libapache2-mod-evasive

Enable the Apache `mod_evasive` module:

    sudo a2enmod mod-evasive

If you have to disable the the Apache `mod_evasive` module, this is how you do it:

    sudo a2dismod mod-evasive

Create a log directory for `mod_evasive`:

    sudo mkdir /var/log/apache2/mod_evasive

Create a sample `mod_evasive` config by creating a `mod_evasive.conf` file like this:

	sudo nano /etc/apache2/conf.d/mod_evasive.conf

Setup a config like this; this is sample config that can be adjusted as needed:

	<ifmodule mod_evasive20.c>
	  DOSHashTableSize 1024
	  DOSPageCount 2
	  DOSSiteCount 50
	  DOSPageInterval 1
	  DOSSiteInterval 1
	  DOSBlockingPeriod 10
	  DOSCloseSocket On
	  DOSLogDir /var/log/apache2/mod_evasive
	  DOSEmailNotify email_address@example.com
	  DOSWhitelist 127.0.0.1
	</ifmodule>

Now just restart Apache to get `mod_evasive` and related settings loaded and you should be good:

	sudo service apache2 restart

### A simple DDoS Perl test script.

The purpose of this Perl script is to create a test DDoS attack on a target server. It’s not sophisticated in any way and fairly clumsy in execution but it works well for simple testing of `mod_evasive`’s DDoS capabilities.

On an unrelated system—like the desktop of a Mac OS X system—create a file named `mod_security_perl_test.pl`:

    nano mod_security_perl_test.pl

Now place this code in that file. Adjust the values of `$how_many` and `$target_host` to match the values that suit your testing needs:

	#!/usr/bin/perl
	
	use IO::Socket;
	use strict;
	
	my $how_many = 30;
	my $target_host = "sandbox.local:80";
	
	for (0..$how_many) {
	  my($response);
	  my($SOCKET) = new IO::Socket::INET(Proto => "tcp", PeerAddr => $target_host);
	  if (!defined $SOCKET) { die $!; }
	  print $SOCKET "GET /?$_ HTTP/1.0\n\n";
	  $response = <$SOCKET>;
	  print $response;
	  close($SOCKET);
	}

Save the file and change the permissions to make it executable:

    chmod 755 mod_security_perl_test.pl

Then run it like this:

    ./mod_security_perl_test.pl

The output will be a pile of headers for each HTTP request that is made; see below for sample output. Note that after about 16 attempts the responses of “200 OK” suddenly switch to “403 Forbidden”; that means that `mod_evasive` has recognized the “attack” and is successfully blocking it:

	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 200 OK
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden
	HTTP/1.1 403 Forbidden

***

*Cheat Sheet - Apache - Mod Evasive (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*