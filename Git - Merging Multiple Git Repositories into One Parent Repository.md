## Git - Merging Multiple Git Repositories into One Parent Repository

By Jack Szwergold, October 18, 2015

This process will allow you to merge two disparate repositories into one parent repository. In this example, the contents of each “child” repository will be stored in directories named `Directory_A` and `Directory_B`. Feel free to adjust specifics to match your projects needs.

### Create a parent project directory.

For this example we’ll create a parent project directory simply named `ParentProject`; feel free to change that name to match your specific needs.

Create the `ParentProject` directory like this:

	mkdir ParentProject

Now go into the `ParentProject` directory:

	cd ParentProject

Init it as a Git repo:

	git init

Create a basic `.gitignore` file that will ignore `.DS_Store` for now:

	echo ".DS_Store" > .gitignore

Add the `.gitignore` file to the repository:

    git add .gitignore

Finally, create the first real commit for the repo:

	git commit -a -m "First real commit."

### Merging `ChildRepositoryA` (`Child_A`) into `Directory_A`.

Now, let’s add a remote branch based on the first repository you want to merge like this:

	git remote add -f Child_A git@github.com:JackSzwergold/ChildRepositoryA.git

Merge it like this:

	git merge -s ours --no-commit Child_A/master

Then run `read-tree` to get the contents of `Child_A` into `Directory_A` like this:

	git read-tree --prefix=Directory_A/ -u Child_A/master

And finally commit it like this:

	git commit -a -m "Merging the contents of Child_A into Directory_A."

### Merging `ChildRepositoryB` (`Child_B`) into `Directory_B`.

Now, let’s add a remote branch based on the second repository you want to merge like this:

	git remote add -f Child_B git@github.com:JackSzwergold/ChildRepositoryB.git

Merge it like this:

	git merge -s ours --no-commit Child_B/master

Then run `read-tree` to get the contents of `Child_B` into `Directory_B` like this:

	git read-tree --prefix=Directory_B/ -u Child_B/master

And finally commit it like this:

	git commit -a -m "Merging the contents of Child_B into Directory_B."

***

*Git - Merging Multiple Git Repositories into One Parent Repository (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*