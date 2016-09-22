## NodeJS - Meteor

By Jack Szwergold

#### Install Meteor.

    curl https://install.meteor.com/ | sh

#### Uninstall Meteor.

    sudo rm /usr/local/bin/meteor
    rm -rf ~/.meteor
    rm ~/.meteorsession

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

Run this command to install the NPM dependencies:

    meteor npm install

Start the application like this:

    meteor

Or run it with a specific release—such as version 1.4.1—like this:

    meteor run --release 1.4.1

#### Installing “Meteor Up” for deployment.

MUP (Meteor Up) is a NodeJS deployment tool for Meteor apps. Install it via NPM like this:

    sudo npm install -g mup

Or install a specific version like this:

    sudo npm install -g mup@0.9.7
    sudo npm install -g mup@0.8.0
    sudo npm install -g mup@0.7.7
    sudo npm install -g mup@0.6.12

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

#### Checking Meteor versions.

Checking the verison of Meteor installed on the system:

    meteor --version

The output of that command should be:

    Meteor 1.2.1

Richer, more detailed version information can be gleaned by running this command:

    meteor show meteor

And the output should be:

	Package: meteor@1.2.17                        
	Maintainers: mdg                              
	Exports: Meteor, global, meteorEnv            
	                                              
	This is an internal Meteor package.           
	                                              
	Recent versions:                              
	  1.1.15    June 10th, 2016                   
	  1.1.15_1  June 23rd, 2016
	  1.1.16    July 7th, 2016
	  1.2.16    July 25th, 2016
	  1.2.17    August 18th, 2016     installed

To check the version of Meteor that is a part of a project—which might be older than the system installed version—just check the contents of `.meteor/release` like this:

    cat .meteor/release

The output should be something like this:

    METEOR@1.2.1

***

*NodeJS - Meteor (c) by Jack Szwergold; written on September 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*