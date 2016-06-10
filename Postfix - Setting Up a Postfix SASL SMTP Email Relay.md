## Setting Up a Postfix SASL SMTP Email Relay

By Jack Szwergold, October 7, 2015

By default, Mac OS X has Postfix installed. But it’s not enabled by default. Not to mention sending emails via a `localhost` MTA will most likely result in the message being tagged as SPAM pretty quickly.

So this cheat sheet explains how you can use an external SMTP account to send emails via Postfix on a local machine. Tested and works well in Mac OS X.

***

### Setting up the Postfix SASL lookup table.

First, we need to create a `sasl_passwd` password file like this:

    sudo nano /etc/postfix/sasl_passwd

In that file we’ll setup a set of SMTP server credentials like this. Here is the overall format:

    [hostname]:[port] [email username]:[password]

Here is another example with some GMail specifics to make it a bit clearer but the same concept can be used for any SMTP service with the correct settings:

    smtp.gmail.com:587 username@gmail.com:password

Now run `postmap` to create the Postfix lookup table (`sasl_passwd.db`) like this:

	sudo postmap /etc/postfix/sasl_passwd

### Configure Postfix to use SASL.

Open up the main Postfix configuration file like this:

	sudo nano /etc/postfix/main.cf

Many of these settings might already be set or actually commented out in that config file, so just append this all to the end of the Postfix configuration file; theer are some GMail specifics—for `relayhost`—to make it a bit clearer but the same concept can be used for any SMTP service with the correct settings:

	# Minimum Postfix-specific configurations.
	mydomain_fallback = localhost
	mail_owner = _postfix
	setgid_group = _postdrop
	relayhost=smtp.gmail.com:587
	
	# Enable SASL authentication in the Postfix SMTP client.
	smtp_sasl_auth_enable=yes
	smtp_sasl_password_maps=hash:/etc/postfix/sasl_passwd
	smtp_sasl_security_options=
	
	# Enable Transport Layer Security (TLS), i.e. SSL.
	smtp_use_tls=yes
	smtp_tls_security_level=encrypt
	tls_random_source=dev:/dev/urandom

Save the file and then start Postfix like this:

	sudo postfix start

You will know the Postfix system is up and running if this message is returned:

    postfix/postfix-script: starting the Postfix mail system

And you can doubly confirm that by running this command:

    sudo postfix status

The response should be something like this:

    postfix/postfix-script: the Postfix mail system is running: PID: 2874

Here are some other Postfix start, stop, control and status options:

	sudo postfix start
	sudo postfix stop
	sudo postfix reload
	sudo postfix status
	sudo postfix check
	sudo postfix set-permissions
	sudo postfix upgrade-configuration

### Testing and debugging the Postfix SASL setup.

The simplest way to check if this setup works is

	echo "Hello, world." | mail -s "Test Subject" email_address@example.com

If the mail doesn’t get sent out right away, check the mail queue like this:

	mailq

If the mails get stuck in that queue and you ned to clear them out, run this command:

	sudo postsuper -d ALL

### Getting Postfix to start on system startup/reboot in Mac OS X.

If you want Postfix to automatically startup when the system starts up or reboots, open up the `org.postfix.master.plist` file in `LaunchDaemons`:

    sudo nano /System/Library/LaunchDaemons/org.postfix.master.plist

And edit the XML that looks like this:

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/Prop$
	<plist version="1.0">
	<dict>
	        <key>Label</key>
	        <string>org.postfix.master</string>
	        <key>Program</key>
	        <string>/usr/libexec/postfix/master</string>
	        <key>ProgramArguments</key>
	        <array>
	                <string>master</string>
	                <string>-e</string>
	                <string>60</string>
	        </array>
	        <key>QueueDirectories</key>
	        <array>
	                <string>/var/spool/postfix/maildrop</string>
	        </array>
	        <key>AbandonProcessGroup</key>
	        <true/>
	</dict>
	</plist>

To be this; note the `<string>-e</string>` and `<string>60</string>` are disabled and `<key>KeepAlive</key><true/>` is added to the bottom:

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/Prop$
	<plist version="1.0">
	<dict>
	        <key>Label</key>
	        <string>org.postfix.master</string>
	        <key>Program</key>
	        <string>/usr/libexec/postfix/master</string>
	        <key>ProgramArguments</key>
	        <array>
	                <string>master</string>
	                <!-- <string>-e</string> -->
	                <!-- <string>60</string> -->
	        </array>
	        <key>QueueDirectories</key>
	        <array>
	                <string>/var/spool/postfix/maildrop</string>
	        </array>
	        <key>AbandonProcessGroup</key>
	        <true/>
	        <key>KeepAlive</key>
	        <true/>
	</dict>
	</plist>

Then you can stop reload that XML config by running this `launchctl unload` command:

	sudo launchctl unload /System/Library/LaunchDaemons/org.postfix.master.plist

Followed by this `launchctl load` command:

	sudo launchctl load /System/Library/LaunchDaemons/org.postfix.master.plist

***

*Setting Up a Postfix SASL SMTP Email Relay (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
