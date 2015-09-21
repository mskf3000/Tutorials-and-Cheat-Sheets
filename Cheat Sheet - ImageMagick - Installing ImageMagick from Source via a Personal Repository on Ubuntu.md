# Cheat Sheet - ImageMagick - Installing ImageMagick from Source via a Personal Repository on Ubuntu

By Jack Szwergold, September 21, 2015

#### Get rid of the default repository installed version of ImageMagick.

    sudo aptitude purge imagemagick

#### Install the prerequisites and dependencies.

    sudo aptitude update

    sudo aptitude install build-essential checkinstall libx11-dev libxext-dev zlib1g-dev libpng12-dev libjpeg-dev libfreetype6-dev libxml2-dev

    sudo aptitude build-dep imagemagick

#### Install ImageMagick from source.

	wget http://www.imagemagick.org/download/ImageMagick.tar.gz
	
	tar -xzvf ImageMagick.tar.gz
	
	cd ImageMagick-*
	
	./configure
	
	sudo checkinstall
	
	sudo ldconfig /usr/local/lib

When all done you will get a message like this on the screen:

	**********************************************************************
	
	 Done. The new package has been installed and saved to
	
	 /home/sysop/ImageMagick-6.9.0-0/imagemagick-6.9.0_0-1_amd64.deb
	
	 You can remove it from your system anytime using:
	
	      dpkg -r imagemagick-6.9.0
	
	**********************************************************************

Uninstall the package:

    sudo dpkg -r imagemagick-6.9.0

***

*Cheat Sheet - ImageMagick - Installing ImageMagick from Source via a Personal Repository on Ubuntu (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*    