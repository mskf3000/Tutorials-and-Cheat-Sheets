# Git - Restoring Lost Commits from Dangling Blobs and Reference Logs

By Jack Szwergold, October 3, 2015

The basic concept here is you dug yourself into a hole by reverting commits. And there is no easy way to “pull” from origin or recover by any other means.

Don’t panic! Hope is not lost! As long as you have not done any “garbage collection” (via: `git gc`), a “dangling blob” might still be there just waiting for you to recover it.

#### Accidentally rolling back commits.

So let‘s create a problem so we can fix it. Let’s look at the last 4 commits with this command:

    git log -n 4

The output should be something like this:

	commit 5dd238dbac9002f1e5e661e19e585a47946dc0b6
	Author: Some Email <email_address@example.com>
	Date:   Fri Oct 2 23:41:39 2015 -0400
	
	    Adding a cheat sheet on restoring a deleted file in a Git repository.
	
	commit e0ad5e1de7cc4d71815030fc5cea7cd0b881e3e2
	Author: Some Email <email_address@example.com>
	Date:   Fri Oct 2 17:21:32 2015 -0400
	
	    Tweaking a cheat sheet on Apache Mod Security items.
	
	commit 883dee5a6d72c340d32ba6f4ff7102cc15b26a22
	Author: Some Email <email_address@example.com>
	Date:   Fri Oct 2 17:18:16 2015 -0400
	
	    Tweaking a cheat sheet on Apache Mod Security items.
	
	commit 64c7b3806eb107ce2352033b06850fd9dfe9c418
	Author: Some Email <email_address@example.com>
	Date:   Fri Oct 2 17:14:11 2015 -0400
	
	    Tweaking a cheat sheet on Apache Mod Security items.

Okay, so we have decided for some crazy reason we want to roll back to commit `64c7b3806eb107ce2352033b06850fd9dfe9c418`. So we now do this:

    git reset --hard 64c7b3806eb107ce2352033b06850fd9dfe9c418

Great! You have now wiped out three commits. Now let’s see how to get them back.

#### Restoring lost commits from dangling blobs and reference logs.

Okay, so you realized you made a big mistake when you rolled back those commits. Fine. Just check in the `fsck --lost-found` and let’s see if anything is in there:

	git fsck --lost-found

And yes, there is a “dangling blob” in there:

	Checking object directories: 100% (256/256), done.
	Checking objects: 100% (946/946), done.
	dangling blob 3ab02616f1755aa9626d66539d9f0605ef06b8a5

So far, so good. But now we need to check the Git reference logs like this to see what’s actually in that blob:

	git reflog

And here is what the output should look like:

	64c7b38 HEAD@{0}: reset: moving to 64c7b3806eb107ce2352033b06850fd9dfe9c418
	5dd238d HEAD@{1}: commit: Adding a cheat sheet on restoring a deleted file in a Git repository.
	e0ad5e1 HEAD@{2}: clone: from git@github.com:JackSzwergold/Cheat-Sheets.git

The wait to interpret that is `HEAD@{0}` was the `git reset --hard` that was just executed so `HEAD@{1}` is what we want to target for recovery. So note the `5dd238d` hash and run this `git merge` command:

	git merge 5dd238d

Once you enter that command, the merge process should run and the commits you thought were lost forever will now magically be recovered.

	Updating 64c7b38..5dd238d
	Fast-forward
	 Cheat Sheet - Apache - Mod Security.md              |  6 ++++--
	 ... Restoring a Deleted File in a Git Repository.md | 21 +++++++++++++++++++++
	 2 files changed, 25 insertions(+), 2 deletions(-)
	 create mode 100644 Cheat Sheet - Git - Restoring a Deleted File in a Git Repository.md

With that done, pat yourself on the back. You just recovered some hard work and learned a new trick.

***

*Git - Restoring Lost Commits from Dangling Blobs and Reference Logs (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
