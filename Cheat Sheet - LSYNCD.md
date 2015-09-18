# Cheat Sheet - LSYNCD

By Jack Szwergold, September 18, 2015

#### Installing `lsyncd`.

Install `lsyncd` via `aptitude` like this:

    sudo aptitude install lsyncd

#### Start, stop and control `lsyncd`.

	sudo service lsyncd start
	sudo service lsyncd stop
	sudo service lsyncd restart
	sudo service lsyncd force-reload

#### Sundry `lsyncd` items.

Create the `lsyncd` log directory:

	sudo mkdir -p /var/log/lsyncd

And now create placeholder `lsyncd.log` and `lsyncd.status` files like this:

	sudo touch /var/log/lsyncd/lsyncd.{log,status}

Follow the `lsyncd` log:

	tail -f -n 100 /var/log/lsyncd/lsyncd.log
	
Follow the `lsyncd` status file:

	tail -f -n 100 /var/log/lsyncd/lsyncd.status

The basic, core `rsync` logic contained in `lsyncd` can be translated as this `rsync` command:

	rsync -ltsd --delete --include-from=- --exclude=* SOURCE TARGET

Check out the sample `lsyncd` config files for ideas:

	ls -la /usr/share/doc/lsyncd/examples
	
The contents of that sample `lsyncd` config directory should look something like this:

	-rw-r--r-- 1 root root 1064 Nov 29  2010 lbash.lua
	-rw-r--r-- 1 root root  577 Nov 18  2010 lecho.lua
	-rw-r--r-- 1 root root 2277 Nov 29  2010 lgforce.lua
	-rw-r--r-- 1 root root 2743 Nov 20  2010 limagemagic.lua
	-rw-r--r-- 1 root root 2621 Feb  8  2011 lpostcmd.lua
	-rw-r--r-- 1 root root  217 Nov 18  2010 lrsync.lua
	-rw-r--r-- 1 root root  204 Nov 16  2010 lrsyncssh.lua

#### Creating a directory structure and test files for basic testing.

Create a basic source directory structure:

	mkdir -p /home/sysop/zzz_source
	
Create a basic destination directory structure:

	mkdir -p /home/sysop/zzz_destination
	
Now create some test files in the source directory using `touch` like this:

	touch /home/sysop/zzz_source/file{1..10}

#### Example 1: Setting up `lsyncd` to use `bash` and `cp` for one-way syncing.

This is an example of a one way sync from source to destination using `bash` and the `cp` command for the core logic. A tad nicer than the internal `rsync` logic `lsyncd` uses since it allows you to control all aspects of copying process.

First step is to create the `/etc/lsyncd` directory to store the active configs if it doesn’t already exist:

	sudo mkdir -p /etc/lsyncd
	
Next, create the `lsyncd.conf.lua` that would go into that `/etc/lsyncd` directory:

	sudo nano /etc/lsyncd/lsyncd.conf.lua

And here is where the magic happens; copy this simple Lua-based script into that file:

	settings = {
		logfile = "/var/log/lsyncd/lsyncd.log",
		statusFile = "/var/log/lsyncd/lsyncd.status",
		statusInterval = 1,
		nodaemon = false,
	}
	
	bash = {
	    delay = 5,
	    maxProcesses = 3,
		onStartup = [[ if [ "$(ls -A ^source)" ]; then cp -p -r ^source* ^target; fi; find ^source -maxdepth 1 -type f -mmin +5 | sort -nr | xargs rm -f ]],
		onCreate = [[cp -p -r ^sourcePath ^targetPathdir; find ^source -maxdepth 1 -type f -mmin +5 | sort -nr | xargs rm -f ]],
		onModify = [[cp -p -r ^sourcePath ^targetPathdir; find ^source -maxdepth 1 -type f -mmin +5 | sort -nr | xargs rm -f ]],
		-- onDelete = [[rm -rf ^targetPath]],
		-- onMove   = [[mv ^o.targetPath ^d.targetPath]],
	}
	
	sync {
		bash,
		source = "/home/sysop/zzz_source",
		target = "/home/sysop/zzz_destination"
	}

#### Example 2: Setting up `lsyncd` to use `rsync` for one-way syncing.

This is an example of a one way sync from source to destination using the core `rsync` logic `lsyncd` is designed to use. This works, but can be a bit limiting if you want a bit more control over the syncing process.

First step is to create the `/etc/lsyncd` directory to store the active configs if it doesn’t already exist:

	sudo mkdir -p /etc/lsyncd
	
Next, create the `lsyncd.conf.lua` that would go into that `/etc/lsyncd` directory:

	sudo nano /etc/lsyncd/lsyncd.conf.lua

And here is where the magic happens; copy this simple Lua-based script into that file:

	sync {
	        default.rsync,
	        source = "/home/sysop/zzz_source",
	        target = "/home/sysop/zzz_destination",
	        rsyncOps = { "-avtOupls", "--delete-after" },
	        delay = 1,
	        group = true,
	        archive = true
	}
	
	settings = {
	        logfile = "/var/log/lsyncd/lsyncd.log",
	        statusFile = "/var/log/lsyncd/lsyncd.status"
	}

#### Testing the one-way sync process.

Once you have the `lsyncd` script in place, check the destination directory; it should be empty:

	ls -la /home/sysop/zzz_destination

Now start the `lsyncd` service:

	sudo service lsyncd start

After the `lsyncd` service has started, check the destination directory again and the contents of the source directory should now successfully be copied there:

	ls -la /home/sysop/zzz_destination

Returned output should be something like this:
	
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file1
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file10
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file100
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file11
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file12
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file13
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file14
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file15
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file16
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file17
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file18
	-rw-rw-r-- 1 sysop www-readwrite    0 Feb 13 20:28 file19

#### Create a `logrotate.d` script for `lsycnd`.

While `lsycnd` creates logs, it doesn’t come with a built in log rotation script. So here’s a basic set of log rotation scripts; one handles `*.log` files and the other handles `*.status` files.

Next, create the `lsyncd` log rotation script that would go into that `/etc/logrotate.d/` directory:

	sudo nano /etc/logrotate.d/lsyncd
	
And copy these simple log rotation scripts into that file:

	/var/log/lsyncd/*.log {
	        weekly
	        missingok
	        rotate 13
	        compress
	        delaycompress
	        notifempty
	        create 640 root www-readwrite
	        sharedscripts
	        postrotate
	        if [ -f /var/run/lsyncd.pid ]; then
	            /usr/sbin/service lsyncd restart > /dev/null 2>/dev/null || true
	        fi
	        endscript
	}
	
	/var/log/lsyncd/*.status {
	        weekly
	        missingok
	        rotate 13
	        compress
	        delaycompress
	        notifempty
	        create 640 root www-readwrite
	        sharedscripts
	        postrotate
	        if [ -f /var/run/lsyncd.pid ]; then
	            /usr/sbin/service lsyncd restart > /dev/null 2>/dev/null || true
	        fi
	        endscript
	}

***

*Cheat Sheet - LSYNCD (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
