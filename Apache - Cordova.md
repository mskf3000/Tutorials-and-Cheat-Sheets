# Apache - Cordova

By Jack Szwergold, March 11, 2016

***

## Cordova related stuff.

#### The basics.

First install Apache Cordova via the command line like this:

	sudo npm install -g cordova

With that done, create a directory for your application like this:

	mkdir test

And go into that directory like this:

	cd test

Create the Cordova app in that directory like this:

	cordova create .

Now setup the `browser` platform like this:

	cordova platform add browser

And run the appliction like this:

	cordova run browser

If you want to set a browser other than the default Chrome—or a different port—just pass through options like this:

	cordova run browser -- --target=safari --port=8000

#### Adding live reload support.

	cordova plugin add cordova-plugin-browsersync

## Ionic related stuff.

Now install Ionic:

	sudo npm install -g ionic

Go into the `test/` platfom directory:

	cd test

Now setup the `browser` platform like this:

	ionic platform add browser

Build the `browser` app:

	ionic build browser

And now run the `browser` app:

	ionic emulate browser

***

*Apache (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*