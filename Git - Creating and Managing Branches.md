## Git - Creating and Managing Branches

By Jack Szwergold, September 15, 2015

### Create a development branch & push that to the origin.

While most git repositories are set to the `master` branch to begin with, that’s not generally where you want to do your work. It’s best to create a separate/new branch to do work in and merge that to `master` when the work is done/tested.

The easiest way to do this is to create a `develop` branch based off of the `master` branch. Let’s do it like this.

First, get yourself into the `master` branch:

	git checkout master

Now checkout a `develop` branhc like this:

	git checkout -b develop

Finally, push the new `develop` branch to the remote origin:

	git push origin develop

Or you can even just use `git push` like this:

	git push

### Basic interactions.

To get commits and changes from a remote repository, just run `git pull` like this:

	git pull

Remove a file from a repository use `git rm` like this:

	git rm [filename]

### List repository branches.

To check your local branches, just run this command:

	git branch

But personally I find that too limiting, so I prefer to use the `-a` (all) flag instead like this so I can see all branches; local and remote:

	git branch -a

And if for some reason you only want to review a list of remote branches, just run this command:

	git branch -r

### How to rename a branch.

Let’s say you now want to rename the `develop` branch to `develop_test`. Just run this command:

	git branch -m develop develop_test

And hey, you decided that was not so hot an idea. So you can rename it again like this:

	git branch -m develop_test develop

### Delete a local branch.

Okay, so let’s say you want to delete a local branch such as `develop`. First, make sure you are in any other branch but `develop` like this:

	git checkout master

Now you can delete the branch with the `-d` flag like this:

	git branch -d develop

That should work if the branch is merged. But if you don’t care about the merged status of the branch just use the uppercase equivalent `-D` like this:

	git branch -D develop

And that branch should be successfully blown away locally.

### Delete a remote branch.

If you want to delete a remote branch, you need to run this command:

	git push origin :develop

Note the colon (`:`) before the branch name of `develop`. That `git push` combined with `origin :develop` will delete the remote `develop` branch.

### Reset a local branch to a remote branch’s state.

Now let’s say you have made some local commits to `develop` that you are not happy with but you have not pushed `develop` to the remote origin at all. Well, then you can reset your local branch to the state of the remote `develop` branch to get everything back to normal again like this:

	git reset --hard origin/develop

### Merge a branch.

First checkout the branch you want to merge into; in this case it will be `master`:

	git checkout master

Now merge the `master` branch with the `develop` branch like this:

	git merge develop

If you want to add a commit point to the merge, just use the `--no-ff` flag like this:

	git merge --no-ff develop

### Check for merged branches.

You can check which local branches are merged with this command:

	git branch --merged

And you can check which remote branches are merged with this command:

	git branch -r --merged

### Check for unmerged branches.

You can check which local branches are unmerged with this command:

	git branch --no-merged

And you can check which remote branches are unmerged with this command:

	git branch -r --no-merged

### Diff branches.

This command will diff the local `master` branch to the local `develop` branch:

	git diff master develop

This command will diff the remote `develop` branch to the local `develop` branch:

	git diff remotes/origin/develop develop

### Check your history of commits.

You can see a list of commits using `git log` as follows; set `-n 10` to be the number of commits you want to see like this:

	git log -n 10

And you can also display a nice, visual branching commit history from the command line using `--oneline --decorate --graph` like this:

	git log --oneline --decorate --graph

### Show the relationships between local & remote branches.

Run this command:

	git remote show origin

And the output should be something like this:

	* remote origin
	  Fetch URL: git@github.com:JackSzwergold/Preworn-Main.git
	  Push  URL: git@github.com:JackSzwergold/Preworn-Main.git
	  HEAD branch: master
	  Remote branches:
	    develop tracked
	    master  tracked
	  Local branches configured for 'git pull':
	    develop merges with remote develop
	    master  merges with remote master
	  Local refs configured for 'git push':
	    develop pushes to develop (up to date)
	    master  pushes to master  (up to date)

### List all references to branches & tags that it knows it about including info on HEADs.

Run this command:

	git ls-remote origin

And the output should be something like this:

	b331419ad4cc4397a3138d3fc1166be014debc4f	HEAD
	b331419ad4cc4397a3138d3fc1166be014debc4f	refs/heads/develop
	b331419ad4cc4397a3138d3fc1166be014debc4f	refs/heads/master
	e5ef5aa6d501a8673a27a8a5fad9ed57f167ad31	refs/pull/1/head

### List all references to branches & tags that it knows it about without info on HEADs.

Run this command:

	git ls-remote --heads origin

And the output should be something like this:

	b331419ad4cc4397a3138d3fc1166be014debc4f	refs/heads/develop
	b331419ad4cc4397a3138d3fc1166be014debc4f	refs/heads/master

### Delete a single stray/old remote branch in a local repo.

Run this command:

	git branch -r -d origin/devel

### Prune old remote branches from a local GIT repo.

By default, a local clone of a remote repository will have copiess of references to remote branches. Sometimes it can get cluttered which makes things hard to manage. So it’s good practics to clean up old, stray branches to make working with your repository easuer.

First run this command with the `--dry-run` flag so you can see exactlty what it will do before it does it:

	git remote prune origin --dry-run

Now run the actual command without the `--dry-run` flag so stray branches can be properly pruned:

	git remote prune origin

### Do some garbage collection and maintenance to repair a damaged local clone.

	git gc --auto
	
	git count-objects -v
	
	git fsck --full

### Cleaning up “dangling blobs” from a repository.

If you run `fsck` like this:

	git fsck --full

And see some “dangling blobs” something like this:

	Checking object directories: 100% (256/256), done.
	Checking objects: 100% (3/3), done.
	dangling blob bf04e8bffd2421d555f3399bb06edda7cb5e2c62
	dangling blob c204d6a0fd696190f7a1d8e2e9217378046190b1
	dangling blob c44e39a0c45916ccc9a35834470cf3ad4fb724dd

You can get rid of them like this. This command gets rid of all references to unreachable commits in `reflog`:

    git reflog expire --expire-unreachable=now --all

And this command removes the commits themselves:

    git gc --prune=now

After doing that you can run `fsck` again like this:

    git fsck --full

And the output will be something like this; note there are no “dangling blobs” in the output:

	Checking object directories: 100% (256/256), done.
	Checking objects: 100% (139/139), done.

***

<sup>*Git - Creating and Managing Branches (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>
