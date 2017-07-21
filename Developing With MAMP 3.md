## Developing With MAMP 2.1.4

By Jack Szwergold

### Part 3: Upgrading the PHP Pear Library in MAMP

Now we’ll install the whole PHP Pear library under MAMP. Yes, the whole PEAR library.

Why? Easy. While MAMP includes a few common items from the PEAR library, it does not include everything. And since I am often dealing with developers—or projects—that make use of PEAR items that are not common to the base MAMP install, I might as well just install the whole PEAR library all at once in MAMP so I don’t have to worry about it.

So if none of that means anything to you, then don’t worry about any of this and stop reading now. Want to know more about how you can upgrade the PEAR librarian MAMP? Read on.

#### Let’s get started.

First, you need to determine what version of PHP you are using. You can figure that out by checking the output of [phpinfo()][1]. In this case I know I am using PHP version 5.4.10. And if you maneuver through the directory structure of MAMP, you can find the path to installed MAMP PHP binaries which should look something like this:

	/Applications/MAMP/bin/php/php5.4.10/bin

And if you look in there you can see the PEAR binary as well:

	/Applications/MAMP/bin/php/php5.4.10/bin/pear

Which is great! Because we need to use that PEAR binary to upgrade. But just make note of that for now. We’ll refer to it later.

#### Checking if PEAR is already installed in your system.

Now we’re going to see if you have PEAR installed in your system. We do this using the UNIX command [`which`][2] which shows the full path of shell commands and related binaries. So just type the following into a terminal window like so:

	which pear

And the output—if you are on a basic Mac OS X install—should be blank. Great! Nothing better than starting things off [tabula rasa][3]!

So now with that established, let’s edit your shell `.bash_profile` file to recognized your MAMP install binaries.

#### Setting your MAMP binary path in your shell.

Now that we know what your MAMP PHP binary path is, let’s edit your `.bash_profile` so it can be properly recognized. So open up your `.bash_profile` file like so:

	bbedit ~/.bash_profile

The contents of the file should be something like this:

	export PATH=/usr/bin

And change the `PATH=` value so it adds the MAMP PHP binary path like this; note the `:` delimiter between the different paths:

	export PATH=/Applications/MAMP/bin/php/php5.4.10/bin:/usr/bin

Now reload the newly edited bash `.bash_profile` in the terminal current terminal window like so:

	source ~/.bash_profile

And then use `which` again:

	which pear

Instead of being blank, the output should be:

	/Applications/MAMP/bin/php/php5.4.10/bin/pear

Great! Now you’re set to upgrade your MAMP install of PEAR!

#### Upgrading PEAR in MAMP.

Okay, the first thing you should know is somehow the PEAR config file in MAMP is broken. So it’s best to delete that off the bat:

	rm /Applications/MAMP/bin/php/php5.4.10/conf/pear.conf

Now do a fetch of the PEAR [`remote-list`][4]—which lists remote packages on a specific PEAR channel—like so:

	pear remote-list

While it might take a while for the list to be returned by that command, you might need to do a [`channel-update`][5] to get PEAR to see the proper channel if it completely bombs out. Just do that like so:

	pear channel-update pear.php.net

Once that is done try running `remote-list` and all should be good:

	pear remote-list

The output of that command should be something like this:

	Channel pear.php.net Available packages:
	========================================
	Package                                   Version
	Auth_HTTP                                 2.1.8
	Auth                                      1.6.4
	Auth_SASL                                 1.0.6
	Etc…
	Etc…
	Etc…

Okay, now we’re in business. Now pipe that `pear remote-list` command into a text file named `pear-pacakge-list` like so:

	pear remote-list | awk '{print $1}' > pear-pacakge-list

Wait for the process to end and then edit the `pear-pacakge-list` to get rid of the unnecessary header stuff that looks like this:

	CHANNEL
	========================================
	PACKAGE

So the the text file now looks something like this:

	Auth_HTTP
	Auth
	Auth_SASL
	Etc…
	Etc…
	Etc…

The goal is to pipe the contents of `pear-pacakge-list` into `pear install` so we install every single PEAR library item. And here is the magic command that handles that:

	cat pear-pacakge-list | xargs -n 1 pear install

This process can take a while depending on how fast your Internet connection is. So I would recommend letting that process churn and revisiting it later.

#### Conclusion.

When the dust settles you should have the complete PEAR library installed in your local MAMP setup. To check, just go to your MAMP install’s PHP library folder in MAMP which is located here:

	/Applications/MAMP/bin/php/php5.4.10/lib/php

And check the size. By default it is usually about 14MB in size. But after this upgrade process it will grow to about 100MB in size.

***

*Developing With MAMP 2.1.4 • Part 3: Upgrading the PHP Pear Library in MAMP (c) by Jack Szwergold; written on February 17, 2014. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*

[1]: http://us3.php.net/phpinfo "Info on phpinfo()."
[2]: http://unixhelp.ed.ac.uk/CGI/man-cgi?which "Info on the Unix 'which' command."
[3]: http://en.wiktionary.org/wiki/tabula_rasa "Dictionary definition of tabula rasa."
[4]: http://pear.php.net/manual/en/pyrus.commands.remotelist.php "Info on the pear 'remote-list' command."
[5]: http://pear.php.net/manual/en/guide.users.commandline.commands.php "Info on pear commands inkling 'channel-update'."