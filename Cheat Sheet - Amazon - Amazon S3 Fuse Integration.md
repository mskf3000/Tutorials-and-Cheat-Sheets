# Cheat Sheet - Amazon - Amazon S3 Fuse Integration

By Jack Szwergold, October 6, 2015

#### Tony Wang’s fork of `s3fs` version 1.59.

The reason you should install Tony Wang]s fork of `s3fs` version 1.59 is that—unlike the main master code—his fork supports folders created by the Amazon S3 control panel.

First, make sure some core PHP development packages are installed like this:

	sudo aptitude install git build-essential libfuse-dev fuse-utils libcurl4-openssl-dev libxml2-dev

Next, clone the source code from Tony Wang’s `s3fs-c` GitHub repository:

	git clone git://github.com/tongwang/s3fs-c.git

Now go into the repository directory:

	cd s3fs-c

Run this `configure` command:

	./configure --prefix=/usr

Once the `configure` process completes, run `make`:

	make

Finally install it by running `sudo make install`:

	sudo make install

#### Create the config file for `s3fs`.

A `root`-only `s3fs` setup would go in here:

	sudo nano /etc/passwd-s3fs

And a  per user `s3fs` setup would go in here:

	nano ~/.passwd-s3fs

And the contents of either file would be something like this:

	# AccessKey:SuperSecretKey
	[AccessKey]:[SuperSecretKey]
	# use_cache=/tmp
	# use_rrs

Remember that the credentials files cannot have lax permissions as this creates a security hole and the `s3fs` setup would enforce it.

So for a `root`-only `s3fs` setup, the credentials file (`/etc/passwd-s3fs`) would be set to `640` like this:

    sudo chmod 640 /etc/passwd-s3fs

And a per user `s3fs` setup, the credentials file (`~/.passwd-s3fs`) would be set to `600` like this:

    chmod 600 ~/.passwd-s3fs

#### Create a parent directory `/opt/cloud_mounts/` if it doesn’t exist.

    sudo mkdir /opt/cloud_mounts

Create a subdirectory Amazon cloud_mounts.

    sudo mkdir /opt/cloud_mounts/amazon

Create directories for each bucket.

    sudo mkdir /opt/cloud_mounts/amazon/s3_bucket_name

Change permissions to allow your user—in this case `sysop`—to access it.

    sudo chown sysop:sysop -R /opt/cloud_mounts/amazon

#### Mounting and unmounting.

Mount `s3_bucket_name` as a root:

    sudo s3fs s3_bucket_name /opt/cloud_mounts/amazon/s3_bucket_name

Mount `s3_bucket_name` as a user:

    s3fs s3_bucket_name /opt/cloud_mounts/amazon/s3_bucket_name

Mount `s3_bucket_name` as a user and allow other users to access it:

    s3fs -o allow_other s3_bucket_name /opt/cloud_mounts/amazon/s3_bucket_name

Ummount `s3_bucket_name` bucket as root:

    sudo umount /opt/cloud_mounts/amazon/s3_bucket_name

#### Adjust the `fuse.conf`.

Adjust the `fuse.conf` file to allow non-root users to use `allow_other` or `allow_root`:

    sudo nano /etc/fuse.conf

And uncomment this line:

    user_allow_other

Might need to add the user—in this case `sysop`—to the `fuse` group:

    sudo adduser sysop fuse

#### Some `fstab` related stuff.

If you want the Amazon S3 mount to come up on startup or reboot, you can add it to the `fstab` like this:

    sudo nano /etc/fstab

An `fstab` example given by Amazon:

    s3fs#your-bucket-name  /mnt  fuse allow_other,_netdev,nosuid,nodev,url=https://s3.amazonaws.com 0 0

Another `fstab` example:

    s3fs#s3_bucket_name /opt/cloud_mounts/amazon  fuse    rw,uid=0,gid=0,allow_other     0 0

#### How to use Rsync with Amazon S3 and `s3fs`.

The problem is that Amazon S3 is not really a filesystem; it’s weird. So modifcation times are wonky on an Amazon S3 bucket. This is not an issue in average day-to-day use, but if you are using Rsync and only want to copy files that have been changed this could be an issue. Tons of files that have not been changed will seem as if they have been changed by Rsync and it will try to recopy them.

To avoid this, use the `–size-only` flag and use the `-r` flag instead of an `-a` flag:

    rsync -vr –size-only /path/to/the/source/ /path/to/the/destination/

Before running that command for real, run it in a “dry run” mode to see exactly what Rsync would be acting on:

    rsync --dry-run -vr –size-only /path/to/the/source/ /path/to/the/destination/

***

*Cheat Sheet - Amazon - Amazon S3 Fuse Integration (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*