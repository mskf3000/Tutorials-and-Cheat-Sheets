## GeoIP

By Jack Szwergold

### Installing the GeoIP program.

Install the basics for the build:

    sudo aptitude install build-essential zlib1g-dev libtool

First grab a compressed archive from an official GeoIP source site:

	curl -O -L http://www.maxmind.com/download/geoip/api/c/GeoIP-latest.tar.gz

Next, decompress the archive like this:

	tar -xf GeoIP-latest.tar.gz

Now go into the decompressed directory:

	cd ./GeoIP*

Run `libtoolize` and `automake -f` like this:

	libtoolize -f

Or if you installed `libtool` ith Homebrew, run `glibtoolize` like this:

	glibtoolize -f

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
	
### Installing the GeoIP databases.

#### Get the GeoIP databases.

    curl -O -L http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz
    curl -O -L http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
    curl -O -L http://geolite.maxmind.com/download/geoip/database/asnum/GeoIPASNum.dat.gz
    curl -O -L http://geolite.maxmind.com/download/geoip/database/GeoIPCountryCSV.zip

#### Move the GeoIP databases to their proper directory.

Create the `GeoIP` directory—if it doesn’t exist—like this:

    sudo mkdir -p /usr/local/share/GeoIP/

Move the databases to `/usr/local/share/GeoIP/`:

    sudo mv ~/GeoIP.dat.gz /usr/local/share/GeoIP/
    sudo mv ~/GeoLiteCity.dat.gz /usr/local/share/GeoIP/
    sudo mv ~/GeoIPASNum.dat.gz /usr/local/share/GeoIP/
    sudo mv ~/GeoIPCountryCSV.zip /usr/local/share/GeoIP/

And decompress the database files like this:

    sudo gzip -d -f /usr/local/share/GeoIP/GeoIP.dat.gz
    sudo gzip -d -f /usr/local/share/GeoIP/GeoLiteCity.dat.gz
    sudo gzip -d -f /usr/local/share/GeoIP/GeoIPASNum.dat.gz
    sudo unzip -o -q -d /usr/local/share/GeoIP/ /usr/local/share/GeoIP/GeoIPCountryCSV.zip

    sudo mv /usr/local/share/GeoIP/GeoLiteCity.dat /usr/local/share/GeoIP/GeoIPCity.dat

    sudo rm /usr/local/share/GeoIP/GeoIPCountryCSV.zip

Adjust the permissions of all of the GeoIP databases to be `root`:

    sudo chown root:root -R "/usr/local/share/GeoIP/"

Check to see if the GeoIP databases are set:

    ls -la /usr/local/share/GeoIP/

***

*GeoIP (c) by Jack Szwergold; written on September 23, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
