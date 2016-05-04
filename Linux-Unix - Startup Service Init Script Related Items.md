## Linux-Unix - Startup Service Init Script Related Items

By Jack Szwergold, October 3, 2015

### Setting up a startup service on Ubuntu/Debian systems.

Use `update-rc.d` to install and remove System-V style init script links on Ubuntu/Debian. For example, to install the startup scripts for Apache just run this command:

	sudo update-rc.d apache2 defaults

To check if the scripts are set, run this `ls` command:

	ls -la /etc/rc*/*apache2

Output should look something like this:

	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc0.d/K20apache2 -> ../init.d/apache2
	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc1.d/K20apache2 -> ../init.d/apache2
	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc2.d/S20apache2 -> ../init.d/apache2
	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc3.d/S20apache2 -> ../init.d/apache2
	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc4.d/S20apache2 -> ../init.d/apache2
	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc5.d/S20apache2 -> ../init.d/apache2
	lrwxrwxrwx 1 root root 17 Oct  3 21:58 /etc/rc6.d/K20apache2 -> ../init.d/apache2

Now if you wanted to remove the startup scripts for Apache just run this command:

	sudo update-rc.d -f apache2 remove

And then the output of that would be something like this:

	Removing any system startup links for /etc/init.d/apache2 ...
	  /etc/rc0.d/K20apache2
	  /etc/rc1.d/K20apache2
	  /etc/rc2.d/S20apache2
	  /etc/rc3.d/S20apache2
	  /etc/rc4.d/S20apache2
	  /etc/rc5.d/S20apache2
	  /etc/rc6.d/K20apache2

### Understanding what and how System-V style init script links work.

#### Preface.

Back in the early days of Linux/Unix, one had to manually set these init script symbolic links. Ultimately you’re better off just using `chkconfig` (CentOS/RedHat) or `update-rc.d` (Ubuntu/Debian) to manage init script links.

Why? It’s because they aren’t simple to manage, tend to be a pain in the ass to balance priorities and the overall concept of what each run level actually means subtly changes between Linux distributions. You’re much better off just letting modern management tools do the “heavy lifting” connected to this process.

So this is just simple explanation of how/why this stuff works and is here mainly for reference. 

#### A simple example.

Here is a simple example of how init script links work:

	sudo ln -s /etc/init.d/apache2 /etc/rc3.d/S20apache2

This would create a symbolic link between the Apache `/etc/init.d/` script  and the `/etc/rc3.d/` “run level” directory. Trying to explain the differences between `/etc/rc0.d/`, `/etc/rc1.d/`, `/etc/rc2.d/`, etc… can be headache inducing. So not delving into that for now.

But I will deconstruct the link structure for `S20apache2`. Here is how it breaks down:

    S20apache2

- **S** Indicates start priority. Could also be `K` to give something a stop priority.
- **20** The loading number in the startup process; the lower the number the higher up in the priority it is.
- **apache2** Simply the name of the service being acted on.

***

<sup>*Linux-Unix - Startup Service Init Script Related Items (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>