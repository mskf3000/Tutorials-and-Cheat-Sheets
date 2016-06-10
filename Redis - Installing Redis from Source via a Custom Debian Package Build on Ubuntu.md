## Redis - Installing Redis from Source via a Custom Debian Package Build on Ubuntu

By Jack Szwergold, September 22, 2015

### Get rid of the default Ubuntu repository installed version of Redis.

Just run this `sudo aptitude purge` command to get rid of any Redis stuff installed via the default Ubuntu repository:

    sudo aptitude purge redis-server

### Install the Redis source code prerequisites and dependencies.

First, run `aptitude update` like this:

    sudo aptitude update

Build the dependencies for the build:

    sudo aptitude build-dep redis-server

### Install Redis from source using `checkinstall`.

First grab a compressed archive from an official ImageMagick source site:

	curl -O -L http://download.redis.io/releases/redis-3.0.4.tar.gz	
Next, decompress the archive like this:

	tar -xf redis-3.*.tar.gz
	
Now go into the decompressed directory:

	cd redis-3.*
	
Finally compile and install it by running `checkinstall`:

	sudo checkinstall

Just punch through the default options presented and any questions that might pop up during the Debian package build.

When all done you will get a message like this on the screen which means Redis is installed:

	**********************************************************************
	
	 Done. The new package has been installed and saved to
	
	 /home/sysop/redis-3.0.4/redis_3.0.4-1_amd64.deb
	
	 You can remove it from your system anytime using: 
	
	      dpkg -r redis
	
	**********************************************************************

After that is done, you may need to configure the dynamic linker run-time bindings:

	sudo ldconfig /usr/local/lib

Finally, check the version number like this:

	redis-server --version

And the output should be something like this:

	Redis server v=3.0.4 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 build=4ff19536730eba7b

### Uninstall Redis from source using `dpkg -r`.

Go into the decompressed directory where the initial ImageMagick install happened:

	cd ~/redis-3.*

Run this `dpkg -r` command to uninstall it:

    sudo dpkg -r redis

And if you ever want to reinstall that Debian package, just run this command:

    sudo dpkg -i redis_3.0.4-1_amd64.deb

***

*Redis - Installing Redis from Source via a Custom Debian Package Build on Ubuntu (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*