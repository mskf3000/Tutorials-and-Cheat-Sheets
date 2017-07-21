## Ruby - Capistrano

By Jack Szwergold

**NOTE:** As of May 15, 2015 this document is for reference only since much of the advice has been obsoleted by a recent upgrade of the deployment scripts to use Capistrano 3; specifically version 3.4.0. Installing Capistrano like this should suffice to get the code deployed:

    sudo gem install capistrano:3.4.0

Then install Highline to make sure SSH passwords are not echoed in the clear when deploying:

    sudo gem install highline

And force a clean reinstall of Nokogiri—a Ruby XML and HTML library—like this:

    sudo gem pristine nokogiri --version 1.6.8

With that done, deployment would be handled as follows:

    cap production deploy

Or if you want to deploy as a different user, just set the `CAP_USER` environment variable like this:

    export CAP_USER=username && cap production deploy

***

The purpose of this document is to explain how to setup `capistrano` on your local system to allow for clean & easy deployment of code from GitHub to a destination server.

### Installing Capistrano

There are only three (3) Ruby GEMs that need to be installed to facilitate `capistrano` on your local machine. But for compatibility reasons they should be specific versions. Details follow.

#### Capistrano’s version should be 2.12 or higher.

Ruby 1.9/1.8 method of installing a specific version of `capistrano`.

    sudo gem install capistrano --no-rdoc --no-ri rdoc-data --version=2.15.5

Ruby 2.0 method of installing a specific version of `capistrano`.

    sudo gem install capistrano:2.15.5 --no-rdoc --no-ri rdoc-data

#### Capistrano EXT’s version should be 1.2.1 or higher.


Ruby 1.9/1.8 method of installing a specific version of `capistrano-ext`.

    sudo gem install capistrano-ext --no-rdoc --no-ri rdoc-data --version=1.2.1

Ruby 2.0 method of installing a specific version of `capistrano-ext`.

    sudo gem install capistrano-ext:1.2.1 --no-rdoc --no-ri rdoc-data

#### Net SSH’s version should be exactly at 2.7.0.

Ruby 1.9/1.8 method of installing a specific version of `net-ssh`.

    sudo gem install net-ssh --no-rdoc --no-ri rdoc-data --version=2.7.0

Ruby 2.0 method of installing a specific version of `net-ssh`.

    sudo gem install net-ssh:2.7.0 --no-rdoc --no-ri rdoc-data

**NOTE:** If Capistrano somehow installs a version of `net-ssh` higher than 2.7.0, the connections will fail saying something like this:

> connection failed for: www.preworn.com (Net::SSH::AuthenticationFailed: Authentication failed for user sysop@www.preworn.com)

This bizarre error comes from versions of `net-ssh` erroneously disabling `KbdInteractiveAuthentication` (aka: keyboard interaction) when `ChallengeResponseAuthentication` is set on your host system. The most viable thing to do is to ensure `net-ssh` is only using version 2.7.0 by uninstalling any other version `capistrano` might install.

    sudo gem uninstall net-ssh --version 2.9.1

Another potential fix that might allow one to use versions of `net-ssh` higher than 2.7.0 is to add this line to the `deploy.rb` file:

    ssh_options[:config] = false

But this fix for one issue might open up the script the new errors stemming from newer versions of `net-ssh`. So best play it safe & just be sure `net-ssh` is the one being used.

### Understanding Capistrano

First, not all repositories use `capistrano` for deployment. But more of them do than not. And the ones that do follow the basic usage pattern outline below. Repositories that have `capistrano` scripts in place will have a file and a directory at their root named as follows:

- **Capfile:** The main `capistrano` file. No need to touch this.
- **config:** The directory that contains the actual `capistrano` deployment scripts.

And if you look in the `config` directory you will find the following files & directories:

- **deploy/production.rb:** The `production` specific deployment script.
- **deploy/staging.rb:** The `staging` specific deployment script.
- **deploy.rb:** The main deployment script.

While those files are all set to do what they have to do to get code deployed properly, feel free to look inside to get a better idea of what they are & how they work. It’s basically a pile of Ruby script code as well as `bash` directives that work together to deploy code. At it’s heart, `capistrano` basically creates a high level method of creating `bash` procedures that effect a deployment.

The code can be confusing at first, but when you look at the code & understand this “recipe” takes over the headache one often faces when manually SSHing or FTPing into a server, the value becomes clear: It helps you smooth over the ugliest, most error prone part of a deployment so deployments can proceed as smoothly as possible.

### Using Capistrano

To use `capistrano` one must already have access to a repository on GitHub as well as have basic SSH access to the destination server. This document won’t go into the detail of how either things are setup, but if you don’t have access to those things you simply cannot deploy code. But if you do have access to GitHub as well as basic SSH access to the destination server, then you simply need to do the following.

First, open up the terminal & go into the locally copied repository. Once in there, you can issue a command as follows to deploy a `staging` branch:

    cap staging deploy

That will allow you to deploy the `staging` branch to the destination server as long as the user name on your local machine matches the user name you are using for SSH access on the remote machine. But in some cases that might not be the case, so it is best to explicitly set the user name for deployment like this:

    cap -s user=sysop staging deploy

Just enter your SSH password for the remote server & you are off.

As for what a successful deployment looks like, it’s actually easier to explain that when things go correctly the text on the screen—mainly green & tan in color—will simply reflect the stages of the deployment as they are done. If an error occurs, red text explicitly indicating there was an error will pop up.

Now errors are not good, but you shouldn’t worry too much. The reality is `capistrano` is very error resilient and at no point will it deploy a “half baked” deployment. Meaning, the deployment won’t be finalized until all steps are clean & error free.

So let’s say your Internet connection is bad & drops out during a deployment. No need to worry about your codebase on the destination server going bad. The old deployment—whenever that was made—will always be in place until a newer deployment is successfully completely.

***

*Ruby - Capistrano (c) by Jack Szwergold; written on September 15, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*