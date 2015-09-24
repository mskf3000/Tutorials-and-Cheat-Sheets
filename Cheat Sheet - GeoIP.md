# Cheat Sheet - GeoIP

By Jack Szwergold, September 23, 2015

## Installing the GeoIP program.

Install the basics for the build:

    sudo aptitude install build-essential zlib1g-dev

First grab a compressed archive from an official GeoIP source site:

	curl -O -L http://www.maxmind.com/download/geoip/api/c/GeoIP-latest.tar.gz

Next, decompress the archive like this:

	tar -xvzf GeoIP-latest.tar.gz

Now go into the decompressed directory:

	cd ./GeoIP*

Run `libtoolize` and `automake -f` like this:

	libtoolize -f

Then run this `configure` command:

	./configure

Once the `configure` process completes, run `make`:

	make

After that run `make check` to make sure the `make` worked as expected:

	make check

Finally install it by running `sudo make install`:

	sudo make install

#### Run `autoreconf` if `libtool` chokes with a version mismatch error.

If the `make` chokes due to a `libtool` version mismatch, do this before running `configure`. First install `dh-autoreconf`:

	sudo aptitude install dh-autoreconf

Then run `autoreconf` like this:

	autoreconf --force --install
	
## Installing the GeoIP databases.

#### Get the GeoIP databases.

    curl -O http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz
    curl -O http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
    curl -O http://geolite.maxmind.com/download/geoip/database/asnum/GeoIPASNum.dat.gz

#### Move the GeoIP databases to the proper directory on Linux systems.

On Linux systems move the databases to `/usr/share/`:

    sudo mv ~/GeoIP.dat.gz /usr/share/GeoIP/
    sudo mv ~/GeoLiteCity.dat.gz /usr/share/GeoIP/
    sudo mv ~/GeoIPASNum.dat.gz /usr/share/GeoIP/

Decompress the database files like this:

    sudo gzip -d -f /usr/share/GeoIP/GeoIP.dat.gz
    sudo gzip -d -f /usr/share/GeoIP/GeoLiteCity.dat.gz
    sudo gzip -d -f /usr/share/GeoIP/GeoIPASNum.dat.gz

    sudo mv /usr/share/GeoIP/GeoLiteCity.dat /usr/share/GeoIP/GeoIPCity.dat

    sudo chown root:root -R /usr/share/GeoIP/

Check to see if the GeoIP databases are set:

    ls -la /usr/share/GeoIP/

#### Move the GeoIP databases to the proper directory on Mac OS X.

On Mac OS X move the databases to `/usr/local/share/`:

    sudo mv ~/GeoIP.dat.gz /usr/local/share/GeoIP/
    sudo mv ~/GeoLiteCity.dat.gz /usr/local/share/GeoIP/
    sudo mv ~/GeoIPASNum.dat.gz /usr/local/share/GeoIP/

And decompress the database files like this:

    sudo gzip -d -f /usr/local/share/GeoIP/GeoIP.dat.gz
    sudo gzip -d -f /usr/local/share/GeoIP/GeoLiteCity.dat.gz
    sudo gzip -d -f /usr/local/share/GeoIP/GeoIPASNum.dat.gz

    sudo mv /usr/local/share/GeoIP/GeoLiteCity.dat /usr/local/share/GeoIP/GeoIPCity.dat

Check to see if the GeoIP databases are set in Mac OS X:

    ls -la /usr/local/share/

***

*Cheat Sheet - GeoIP (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
