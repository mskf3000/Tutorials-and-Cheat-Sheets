## NodeJS - Meteor

By Jack Szwergold

#### Install Meteor.

    curl https://install.meteor.com/ | sh

#### Quickstart a Meteor app.

First create a simple “TODO” application:

	meteor create ~/simple-todos

This will create a new directory with this layout:

	client/main.js        # a JavaScript entry point loaded on the client
	client/main.html      # an HTML file that defines view templates
	client/main.css       # a CSS file to define your app's styles
	server/main.js        # a JavaScript entry point loaded on the server
	package.json          # a control file for installing NPM packages
	.meteor               # internal Meteor files
	.gitignore            # a control file for git

With that done, now go into the `simple-todos` directory like this:

    cd ~/simple-todos

Now run this command to install the NPM dependencies:

    meteor npm install

And start the application like this:

    meteor

#### Installing “Meteor Up” for deployment.

MUP (Meteor Up) is a NodeJS deployment tool for Meteor apps. Install it via NPM like this:

    sudo npm install -g mup

You might need to install the NPM SSH2 module like this:

    sudo npm install -g ssh2

Other commands include:

    mup reconfig
    mup stop
    mup start
    mup restart
    mup logs [-f --tail=50]

Check what version of MUP you are using by running this command:

    meteor npm view mup

***

*NodeJS - Meteor (c) by Jack Szwergold; written on September 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*