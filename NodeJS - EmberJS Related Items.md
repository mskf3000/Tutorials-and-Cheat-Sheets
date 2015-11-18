# NodeJS - EmberJS Related Items

By Jack Szwergold, November 16, 2015

***

Before anything, you need to be sure NodeJS is installed. The process is different and dependent on what OS you are using. For Mac OS X you can just download a package installer. But once installed you can then complete the following steps.

***

#### Installing and using EmberJS via NodeJS.

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

#### Getting an EmberJS app ready for production.

So far, all of that fiddling is for a development version of an EmberJS app. But if you really want it to run on a production server—such as Apache—you need to run this command:

    ember build --environment=production

Which would then create a new subfolder in the directory called `dist/`. That—for all intents and purposes—would be the web root for the application.

Copy or deploy that directory where it is needed, set `dist/` as the web root and just go to the regular URL of that web server and your EmberJS app should be good to go.

#### Cleaning out caches and temp files.

Sometimes an upgrade or clean install of EmberJS will mean you need to clean out and reinstall old cruft and dependencies. This is how you go about that.

First, clear out the temp files, Node modules and Bower components:

    rm -rf node_modules tmp dist bower_components

Next, install the NPM dependencies:

    npm install

Finally, install the Bower dependencies:

    bower install

#### Generating a component structure in Ember CLI.

Let’s say we want to create a component structure for an Ember project. And in this case the component would be called `gravatar-image`. Just run this command:

    ember g component gravatar-image

The output of that command would be as follows:

	version: 1.13.12
	installing component
	  create app/components/gravatar-image.js
	  create app/templates/components/gravatar-image.hbs
	installing component-test
	  create tests/integration/components/gravatar-image-test.js

The JavaScript component logic would go into `app/components/gravatar-image.js` and the HTML tempting logic would go into `app/templates/components/gravatar-image.hbs`. Tests are test and would be set in `tests/integration/components/gravatar-image-test.js` but nothing to really worry about in there just now.

***

*NodeJS - EmberJS Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*