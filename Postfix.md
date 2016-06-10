## Postfix

By Jack Szwergold, September 17, 2015

### Check if `postfix` is running in Mac OS X.

In Mac OS X this checks to see what services are launched at startup; look for `org.postfix.master`:

    sudo launchctl list

Or just run that command with `grep` to see if Postfix is running:

    sudo launchctl list | grep postfix

The output if it’s running should be something like this:

    -	0	org.postfix.master

### Sundry `postfix` status and maintenance items.

Check the running processes:

    ps aux

Get the version number of installed postfix:

	sudo postconf -d | grep mail_version

The main `postfix` configuration file:

    sudo nano /etc/postfix/main.cf

Use `netcat` to see if `postfix` is running on port 25:

    nc -w 1 localhost 25 </dev/null

View the active postfix configuration:

    postconf -n

View the default `postfix` config; filter items with `mynetworks` via grep.

    postconf -d | grep mynetworks

Reconfigure postfix. Useful if you need to change host/domain of `from:`:

    sudo dpkg-reconfigure postfix

### Edit the hostname and related details in `postfix`.

Open up the `main `postfix` configuration file:

    sudo nano /etc/postfix/main.cf

Edit the `myhostname` to match the server hostname:

    myhostname = sandbox.local

And similarly, edit the `mydestination` to match the server hostname as well:

    mydestination = sandbox.local, localhost.localdomain, , localhost

### Start, stop and control `postfix`.

	sudo postfix start
	sudo postfix stop
	sudo postfix reload
	sudo postfix condrestart
	sudo postfix status
	sudo postfix check
	sudo postfix set-permissions
	sudo postfix upgrade-configuration

Use `abort` to abruptly stop `postfix`:

    sudo postfix abort

Use these commands to force deferred messages to be flushed from the system and sent:

	sudo postfix flush
	sudo postqueue -f

Checks `postfix` directory structures and permissions:

    sudo postfix check

Sets `postfix` directory structures and permissions:

    sudo postfix set-permissions

Upgrades and checks the `postfix` configuration; warnings can be ignored:

	sudo postfix upgrade-configuration

### Count messages in various message queues/spools.

Count active messages in the postfix queue/spool:

    sudo find /var/spool/postfix/active/. ! -name . ! -name '?' -print | wc -l

Count bounced messages in the postfix queue/spool:

    sudo find /var/spool/postfix/bounce/. ! -name . ! -name '?' -print | wc -l

Count corrupt messages in the postfix queue/spool:

    sudo find /var/spool/postfix/corrupt/. ! -name . ! -name '?' -print | wc -l

Count deferred messages in the postfix queue/spool:

    sudo find /var/spool/postfix/deferred/. ! -name . ! -name '?' -print | wc -l

Count flushed messages in the postfix queue/spool:

    sudo find /var/spool/postfix/flush/. ! -name . ! -name '?' -print | wc -l

Count incoming messages in the postfix queue/spool:

    sudo find /var/spool/postfix/incoming/. ! -name . ! -name '?' -print | wc -l

Count held messages in the postfix queue/spool:

    sudo find /var/spool/postfix/hold/. ! -name . ! -name '?' -print | wc -l

Count clientmqueue messages in the sendmail queue/spool:

    sudo find /var/spool/clientmqueue/. ! -name . ! -name '?' -print | wc -l

### Sundry mail and `postfix` related stuff.

Follow the mail log.

     sudo tail -f -n 200 /var/log/mail.log

Shows which queues have mail being 'processed'.

    mailq

Checks `clientmqueue`:

    sudo mailq -v -Ac

For what it’s worth, this is what `mailq` actually references when it’s run:

    sudo postqueue -p

Force processing of the mail queue:

    sudo sendmail -q

Force processing of the mail queue every 5 minutes:

    sudo sendmail -q5m

To remove all mail from all of the mail queues:

    sudo postsuper -d ALL

To remove all mail from a specific mail queue such as `deferred`:

    sudo postsuper -d ALL deferred

### Mail system user related stuff.

I honestly don’t remember using this stuff but it’s here for reference anyway.

Update the `/etc/aliases.db` database.

    sudo /usr/bin/newaliases

Remove old SMTP SASL/TLS databases:

    sudo rm -rf /etc/postfix/smtp_sasl_passwords.db
    sudo rm -rf /etc/postfix/smtp_tls_sites.db

Update the SMTP SASL/TLS databases:

    sudo postmap /etc/postfix/smtp_sasl_passwords
    sudo postmap /etc/postfix/smtp_tls_sites

### Some ways to test the sending of emails from the command line.

A simple test using `mail` from the command line:

    echo "Hello, world." | mail -s "Test Subject" email_address@example.com

A simple test using `sendmail` from the command line:

	printf "Subject: Hello, world.\n\nA quick brown fox jumped over the lazy dog." | sendmail -f email_address@example.com email_address@example.com

A more intricate test using `sendmail` from the command line:

	printf "From: Example Email <email_address@example.com>\nTo: email_address@example.com\nSubject: Hello, world.\n\nA quick brown fox jumped over the lazy dog.\n" | /usr/sbin/sendmail -F "Example Email" -f "email_address@example.com" "email_address@example.com"

***

*Postfix (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*