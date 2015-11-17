# Node.js - Ember.js Related Items

By Jack Szwergold, November 16, 2015

***

Before anything, you need to be sure Node.js is installed. The process is different and dependent on what OS you are using. For Mac OS X you can just download a package installer. But once installed you can then complete the following steps.

***

#### Installing and using Ember.js via Node.js.

Install the Ember CLI like this:

    sudo npm install -g ember-cli

Once that’s installed you can create your first app by just running this command:

    ember new my-app

With that done, just go into your app directory like this:

    cd my-app

And start the Ember app like this:

    ember serve

If that runs successfully, this will be the output:

	version: 1.13.12
	Could not find watchman, falling back to NodeWatcher for file system events.
	Visit http://www.ember-cli.com/user-guide/#watchman for more info.
	Livereload server on http://localhost:49152
	Serving on http://localhost:4200/

So just go to the URL `http://localhost:4200/` in any web browser and the output should be something like this:

    Welcome to Ember

If you want to do simple edits to the content of that page just open up `app/templates/application.hbs` like this:

    nano app/templates/application.hbs

The content of that sample file should look like this:

	<h2 id="title">Welcome to Ember</h2>
	
	{{outlet}}

To add an input text field that will allow you to customize the welcome name just change it to this:

	<h2 id="title">Welcome to Ember {{name}}!</h2>
	
	<div>
	 <label>Name:</label>
	 {{input type="text" value=name placeholder="Enter your name"}}
	</div>
	
	{{outlet}}

***

*Node.js - Ember.js Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*