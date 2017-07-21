## Git - Rewinding to a Specific Commit

By Jack Szwergold

### Rewinding to a specific commit hash in the repository.

Check the log for the commit hash.

    git log

Find a commit to roll back.

	commit b331419ad4cc4397a3138d3fc1166be014debc4f
	Author: Firstname Lastname <email_address@example.com>
	Date:   Sat May 16 21:31:20 2015 -0400
	
	    Setting a short name for the application; useful for deployments.

Reset the repo to that commit.

    git reset --hard b331419ad4cc4397a3138d3fc1166be014debc4f

Now push that commit back to GitHub.

    git push -f

***

*Git - Rewinding to a Specific Commit (c) by Jack Szwergold; written on September 15, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
