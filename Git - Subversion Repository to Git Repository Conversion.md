# Git - Subversion Repository to Git Repository Conversion

By Jack Szwergold, October 8, 2015

***

### Install the `svn2git` Ruby GEM like this:

Install the `svn2git` Ruby GEM:

    sudo gem install svn2git --no-rdoc --no-ri

With that installed, move on to the preparatory process.

### Prepare the Subversion author list map file for Git usage.

While the Subversion repository to Git repository process is pretty clean, the one thing you need to do before you start is to map the user names from the Subversion repository to something that matches users in the Git repository. Here is how you would do this.

First, in the Subversion cloned directory, run this command to get a list of authors connected to the Subversion repository:

	svn log --xml | grep author | sort -u | perl -pe 's/.*>(.*?)<.*/$1 = /'

Take that output and place it in file called `svn-authors.txt` like this:

    userone
    usertwo

And edit that file manually to map those users to their Git equivalents like this:

	userone = User One <userone@example.com>
	usertwo = User Two <usertwo@example.com>

Save that `svn-authors.txt` and move onto the next steps.

### Create the new Git repository and run the conversion.

Create a directory of the new Git repository:

    mkdir Some-Project

Go into that `Some-Project` directory:

    cd Some-Project

Now, create a Git repository in that directory:

    git init

Make note of the source Subversion repository URL:

    https://svn.example.com/repo/Some-Project/trunk/

And run `svn2git` within the Git repository directory to let the magic begin:

    svn2git https://svn.example.com/repo/Some-Project/trunk/ --authors svn-authors.txt --verbose --rootistrunk

Note the placement of the Subversion repository URL and the `svn-authors.txt` text file in that `svn2git`.

### Post-conversion cleanup.

Now checkout the `trunk` like this:

    git checkout trunk

The directory should be filled up with Subversion content now. Tweak and adjust content as needed. One of the first things you should do is create a `.gitignore` file in the Git repository root like this:

    nano .gitignore

And set the content such as this:

    .DS_Store

Commit that and move on with your life happily living Subversion behind.

***

*Ubuntu - Updates and Repository Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*