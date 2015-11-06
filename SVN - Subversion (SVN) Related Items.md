# SVN - Subversion (SVN) Related Items

By Jack Szwergold, October 8, 2015

#### Adding a user to a Subversion repository.

Adding a user to the Subversion basic authentication file:

    sudo htpasswd -md /etc/svn-auth-file [username]

#### A simple Subversion usage example

The URL for an example repository:

    https://svn.example.com/repo/Some-Project/trunk/

Checkout the repository:

    svn co --username=userone https://svn.example.com/repo/Some-Project/trunk/ .

Go into the repository directory:

	cd Some-Project

Create a test file with `touch`:

	touch newfile.txt

Add that file to the repository:

	svn add newfile.txt

Commit `newfile.txt` and add a message/note about the commit:

    svn commit -m "Adding a file" newfile.txt

Remove a file from the Subversion repository:

    svn rm oldfile.txt

Move/rename a file from the Subversion repository:

    svn mv newfile.txt newfile-newname.txt

#### Force all files into a Subversion repository.

Forces all files to be added:

	svn add --force *

Commit all files via `*` and add a message/note about the commit:

	svn commit -m "First commit for content edit purposes." *

***

*SVN - Subversion (SVN) Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
