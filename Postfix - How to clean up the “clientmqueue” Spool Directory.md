# Postfix - How to clean up the “clientmqueue” Spool Directory

By Jack Szwergold, September 17, 2015

***

Basic `/var/spool/clientmqueue` details if you need them for reference:

- **directory path:** `/var/spool/clientmqueue`
- **user:** `smmsp`
- **group:** `smmsp`
- **permissions before:** `drwxrwx---`
- **permissions after:** `drwxrwx--x`

#### Steps to cleanup the `clientmqueue` directory.

First go into the `/var/spool/` directory:

	cd /var/spool/

Now set the permissions on the `clientmqueue/` directory to be world executable:

    sudo chmod a+x /var/spool/clientmqueue/

With that done, do a TAR backup of the `clientmqueue/` to your home directory just in case; better be safe than sorry:

	sudo tar -cf ~/clientmqueue.tar clientmqueue

Compress that TAR file with GZIP to save some space:

	gzip ~/clientmqueue.tar

Once all of that that is done, go into the `clientmqueue/` directory itself:

    cd clientmqueue

First, run these `find` commands to do a dry run test of the `find` command logic with `echo` before actually ditching the files:

	sudo find . -type f -name "qfp*" -exec echo {} \;
	sudo find . -type f -name "dfp*" -exec echo {} \;
	sudo find . -type f -name "qfq*" -exec echo {} \;
	sudo find . -type f -name "dfq*" -exec echo {} \;

Run these `find` commands to do an interactive run of the `find` command logic to delete the files:

	sudo find . -type f -name "qfp*" -exec rm -i {} \;
	sudo find . -type f -name "dfp*" -exec rm -i {} \;
	sudo find . -type f -name "qfq*" -exec rm -i {} \;
	sudo find . -type f -name "dfq*" -exec rm -i {} \;

Run these `find` commands to do an straight sweep of the `find` command logic and just delete all of the files:

	sudo find . -type f -name "qfp*" -exec rm {} \;
	sudo find . -type f -name "dfp*" -exec rm {} \;
	sudo find . -type f -name "qfq*" -exec rm {} \;
	sudo find . -type f -name "dfq*" -exec rm {} \;

Once that is all cleared up and done, back out of `clientmqueue` and back into `/var/spool/`:

	cd /var/spool/

And set the permissions back to what they were at the beginning of the process:

	sudo chmod o-x /var/spool/clientmqueue/

***

*Postfix - How to clean up the “clientmqueue” Spool Directory (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*