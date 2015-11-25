# EmberJS - Installation and Usage

By Jack Szwergold, November 16, 2015

***

Before anything, you need to be sure NodeJS is installed. The process is different and dependent on what OS you are using. For Mac OS X you can just download a package installer. But once installed you can then complete the following steps.

***

#### Installing and using EmberJS via NodeJS.

Install the Ember CLI like this:

    sudo npm install -g ember-cli

And you can upgrade it like this:

    sudo npm upgrade -g ember-cli

Or even uninstall it like this:

    sudo npm uninstall -g ember-cli

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

    rm -rf tmp dist node_modules bower_components

Next, reinstall the NPM and Bower dependencies:

    npm install && bower install

#### Installing MD5 support.

To enable MD5 support in EmberJS, first run this Bower command:

    bower install --save JavaScript-MD5

Then install Ember CLI MD5 support like this:

    npm install ember-cli-md5

Now just run this Ember `generate` command and you should be good to go:

    ember generate md-5

#### Adjusting URL type in Ember CLI

By default, EmberCLI will use the `location:` type of `auto`. But if for some reason you need to adjust that just go into `config/environment.js` and adjust `locationType` in this config area:

	module.exports = function(environment) {
	  var ENV = {
	    modulePrefix: 'ember-sandbox',
	    environment: environment,
	    baseURL: '/',
	    locationType: 'auto',
	    EmberENV: {
	      FEATURES: {
	        // Here you can enable experimental features on an ember canary build
	        // e.g. 'with-controller': true
	      }
	    },

Just change that `locationType:` to `history` or `none` if your app requires it.

#### Adding meta tag support.

Just install `ember-meta-meta` support like this:

    ember install ember-meta-meta

### Using Ember CLI to create structural components.

#### Generating a “component” structure in Ember CLI.

Let’s say we want to create a component structure for an Ember project. And in this case the component would be called `gravatar-image`. Just run this command:

    ember generate component gravatar-image

The output of that command would be as follows:

	version: 1.13.12
	installing component
	  create app/components/gravatar-image.js
	  create app/templates/components/gravatar-image.hbs
	installing component-test
	  create tests/integration/components/gravatar-image-test.js

The JavaScript component logic would go into `app/components/gravatar-image.js` and the HTML tempting logic would go into `app/templates/components/gravatar-image.hbs`. Tests are test and would be set in `tests/integration/components/gravatar-image-test.js` but nothing to really worry about in there just now.

#### Generating a “routing” structure in Ember CLI.

Let’s say we want to create a routing structure for an Ember project. And in this case the route would be called `routing-example`. Just run this command:

    ember generate route routing-example

The output of that command would be as follows:

	version: 1.13.12
	installing route
	  create app/routes/routing-example.js
	  create app/templates/routing-example.hbs
	updating router
	  add route routing-example
	installing route-test
	  create tests/unit/routes/routing-example-test.js

The JavaScript component logic would go into `app/routes/routing-example.js` and the HTML tempting logic would go into `app/templates/routing-example.hbs`. Tests are test and would be set in `tests/unit/routes/routing-example-test.js` but nothing to really worry about in there just now.

#### Generating a “model” structure in Ember CLI.

Let’s say we want to create a model structure for an Ember project. And in this case the route would be called `pull-requests`. Just run this command:

    ember generate model pull-requests

The output of that command would be as follows:

	version: 1.13.12
	installing model
	  create app/models/pull-requests.js
	installing model-test

The JavaScript component logic would go into `app/models/pull-requests.js`.

***

*EmberJS - Installation and Usage (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*