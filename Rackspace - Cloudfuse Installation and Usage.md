# Rackspace - Cloudfuse Installation and Usage

By Jack Szwergold, October 7, 2015

CloudFuse allows you to mount Rackspace Cloud Files as a mountable fuse filesystem.

#### Install Cloudfuse.

Install the basics for the build:

	sudo aptitude install git build-essential libcurl4-openssl-dev libxml2-dev libssl-dev libfuse-dev fuse-utils

Next, clone the source code from redbo’s `cloudfuse` GitHub repository:

	git clone git://github.com/redbo/cloudfuse

Now go into the repository directory:

	cd cloudfuse

Run this `configure` command:

	./configure --prefix=/usr
	
Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

#### Create the credentials file for Cloudfuse.

A per user Cloudfuse configuration would go in a `.cloudfuse` file:

	nano ~/.cloudfuse

And the contents of either file would be something like this:

	username=[username value in here]
	api_key=[key value in here]
	# authurl=https://auth.api.rackspacecloud.com/v1.1/auth
	authurl=https://auth.api.rackspacecloud.com/v1.0

#### Setup the mount point directories.

Create a subdirectory for Rackspace specific stuff in `cloud_mounts`:

	sudo mkdir -p /opt/cloud_mounts/rackspace

Change permissions to allow your user—in this case `sysop`—to access it:

	sudo chown sysop:sysop -R /opt/cloud_mounts

#### Mounting and unmounting.

To mount a RackSpace mount:

	cloudfuse /opt/cloud_mounts/rackspace

To unmount a RackSpace mount:

	fusermount -u /opt/cloud_mounts/rackspace

#### Restrict Cloudfuse to one user.

First change ownership of the binary to one user:

	sudo chown sysop /usr/local/bin/cloudfuse

Next change the permissions to restrict read, write and execute to only that one user:

	sudo chmod 700 /usr/local/bin/cloudfuse

***

### Cloudfuse README for reference.

	Cloudfuse is a FUSE application which provides access to Rackspace's
	Cloud Files (or any installation of Swift).
	
	Cloud Files is a remote storage system which is similar in principle to
	Amazon S3.  It provides a simple RESTful interface to storing and retrieving
	objects.
	
	    http://www.rackspace.com/cloud/files/
	
	Swift, the software behind Cloud Files, has been open-sourced as part of the
	OpenStack project.
	
	    http://swift.openstack.org/
	
	
	BUILDING:
	
	    You'll need libcurl, fuse, libssl, and libxml2 (and probably their dev
	    packages) installed to build it.  From a base Debian or Ubuntu install,
	    this should get you to a point you can build and run it:
	        apt-get install build-essential libcurl4-openssl-dev libxml2-dev \
	             libssl-dev libfuse-dev
	
	    For CentOS or similar,
	        yum install gcc make fuse-devel curl-devel libxml2-devel openssl-devel
	
	    Cloudfuse is built and installed like any other autoconf configured code.
	    Normally,
	        ./configure
	        make
	        sudo make install
	
	    But I'm no autoconf wizard, and there may be dragons lurking there.
	
	
	USE:
	
	    You'll need to install fuse to use this application. It may have already
	    been installed as a dependency if you followed the "BUILDING" instructions
	    above.
	
	    On Debian:
	       apt-get install fuse
	
	    On CentOS or similar:
	       yum install fuse
	
	    The following settings can be defined in the file ~/.cloudfuse:
	        username=[Account username for authentication, required]
	        api_key=[API key for authentication with Rackspace]
	        tenant=[Tenant name for authentication with Openstack]
	        password=[Authentication password with Openstack]
	        authurl=[Authentication url, defaults to Rackspace's cloud]
	        region=[Regional endpoint to use]
	        use_snet=[True to use Rackspace ServiceNet for connections]
	        cache_timeout=[Seconds for directory caching, default 600]
	        verify_ssl=[False to disable SSL cert verification]
	
	    For authenticating with Rackspace's cloud, at minimum "username" and
	    "api_key" must be set.
	
	    For authenticating with Keystone, "username", "password", "tenant", and
	    "authurl" should probably be defined.
	
	    These settings can also be specified as mount options on the command line:
	        cloudfuse -o username=redbo,api_key=713aa... mountpoint/
	
	    Or as mount options in /etc/fstab:
	        cloudfuse /mnt/cloudfiles fuse username=redbo,api_key=713aa...,user 0 0
	
	    It also inherits a number of command-line arguments and mount options from
	    the Fuse framework.  The "-h" argument should provide a summary.
	
	
	EXAMPLE:
	
	    A typical ~/.cloudfuse configuration file for use with Rackspace:
	        username=demo
	        api_key=643afce8b5187d40ba15e4827384fc5b
	
	        # if no region is selected, it will use your default region.
	        #   region=DFW
	
	        # if connecting within a Rackspace datacenter, ServiceNet can be
	        # used to avoid bandwidth charges.
	        #   use_snet=true
	
	    A typical ~/.cloudfuse configuration file for use with OpenStack,
	    noting that "tenant" should be the tenant name, rather than the ID:
	        username=demo
	        tenant=demo
	        password=supersecret
	        authurl=http://10.10.0.1:5000/v2.0
	
	
	BUGS/SHORTCOMINGS:
	
	    * rename() doesn't work on directories (and probably never will).
	    * When reading and writing files, it buffers them in a local temp file.
	    * It keeps an in-memory cache of the directory structure, so it may not be
	      usable for large file systems.  Also, files added by other applications
	      will not show up until the cache expires.
	    * The root directory can only contain directories, as these are mapped to
	      containers in cloudfiles.
	    * Directory entries are created as empty files with the content-type
	      "application/directory".
	    * Cloud Files limits container and object listings to 10,000 items.
	      cloudfuse won't list more than that many files in a single directory.
	
	
	AWESOME CONTRIBUTORS:
	
	    * Tim Dysinger                                 https://github.com/dysinger
	    * Chris Wedgwood                               https://github.com/cwedgwood
	    * Nick Craig-Wood                              https://github.com/ncw
	    * Dillon Amburgey                              https://github.com/dillona
	    * Manfred Touron                               https://github.com/moul
	    * David Brownlee                               https://github.com/abs0
	    * Mike Lundy                                   https://github.com/novas0x2a
	    * justinb                                      https://github.com/justinsb
	
	
	Thanks, and I hope you find it useful.
	
	Michael Barton
	<mike@weirdlooking.com>

***

*Rackspace - Cloudfuse Installation and Usage (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*