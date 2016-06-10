## Postfix - Delete Specific Messages from the Mail Queue

By Jack Szwergold, September 17, 2015

### A nice Perl script  to flush the mail queue. 

A nice Perl script that deletes messages from the mail queue that matches a regular expression passed to the script as the first argument.

To create the script open up a text file named `postfix-delete.pl`:

    nano postfix-delete.pl

And copy the script into that file:

	#!/usr/bin/perl
	 
	$REGEXP = shift || die "no email-address given (regexp-style, e.g. bl.*\@example.com)!";
	 
	@data = qx</usr/sbin/postqueue -p>;
	for (@data) {
	  if (/^(\w+)(\*|\!)?\s/) {
	     $queue_id = $1;
	  }
	  if($queue_id) {
	    if (/$REGEXP/i) {
	      $Q{$queue_id} = 1;
	      $queue_id = "";
	    }
	  }
	}
	 
	#open(POSTSUPER,"|cat") || die "couldn't open postsuper" ;
	open(POSTSUPER,"|postsuper -d -") || die "couldn't open postsuper" ;
	 
	foreach (keys %Q) {
	  print POSTSUPER "$_\n";
	};
	close(POSTSUPER);

Now save the file and set the permissions to something readable and executable by you like this:

	chmod 700 postfix-delete.pl

### Some usage examples of the `postfix-delete.pl` script.

For example, to delete all queued messages from or to the domain `example.com`, enter:

    ./postfix-delete.pl example.com

And to delete all queued messages that contain the characters `xyz` in the email address:

    ./postfix-delete.pl xyz

***

*Postfix - Delete Specific Messages from the Mail Queue (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*