# Cheat Sheet - Node.js - Installing PhantomJS via a NPM on Ubuntu

By Jack Szwergold, September 22, 2015

### Install PhantomJS via NPM.

Install PhantomJS via NPM like this:

    sudo npm install -g phantomjs

Check the version number of Node.js like this:

    phantomjs --version

And the output should be something like this:

    1.9.8

If somehow that fails, then set the registry like this:

    npm config set registry http://registry.npmjs.org/

And if there are SSL issues, disable the strict SSL settings:

    npm config set strict-ssl false

### Install PhantomJS from source.

#### Fair Warning! This is for reference only and not recommended.

Installing PhantomJS from source is *not recommended* because it can be installed via precompiled packages from elsewhere and as the developer’s state:

> Building PhantomJS from source takes a very long time, anywhere from 30 minutes to several hours (depending on the machine configuration). It is recommended to use the pre-made binary packages on supported operating systems.

So that said, if you really want to build PhanthomJS from source code here is what you have to do.

First, run `aptitude update` like this:

    sudo aptitude update

Install the basics for the build:

	sudo aptitude install build-essential chrpath git-core libssl-dev libfontconfig1-dev libxft-dev

Get the latest source code fro the GitHub repository:

	git clone git://github.com/ariya/phantomjs.git

Now go into the repository directory:

    cd phantomjs

Checkout version 1.9 from the repository:

    git checkout 1.9

Run the `build.sh` script:
	
	./build.sh

The script will warn you:

> Building PhantomJS from source takes a very long time, anywhere from 30 minutes to several hours (depending on the machine configuration). It is recommended to use the pre-made binary packages on supported operating systems.

Just say `y` to that and wait. And wait. And wait.

***

*Cheat Sheet - Node.js - Installing PhantomJS via a NPM on Ubuntu (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*