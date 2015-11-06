# Amazon - Amazon Web Service Command Line Interface

By Jack Szwergold, October 7, 2015

***

#### First, install the PIP package manager for the Python package index.

Download the `get-pip.py` script:

	curl -O -L https://bootstrap.pypa.io/get-pip.py > get-pip.py

And run it through Python like this:

	sudo python get-pip.py

Verify your PIP installation works by running this simple “help” command.:

    pip --help

#### Now, install Amazon Web Service CLI with PIP.

With PIP installed, use PIP to install the Amazon Web Service CLI:

    sudo pip install awscli

Upgrade an existing Amazon Web Service CLI installation, use the `--upgrade` option:

     sudo pip install --upgrade awscli

Once it’s installed, you can configure it with this “wizard”-like command:

    aws configure

It will ask you for something like this:

	AWS Access Key ID [None]: ABCDEFGHIJKLMNOPQRST
	AWS Secret Access Key [None]: AbcdEfghiJklmnOpqrstUvwxyZ1234567890
	Default region name [None]: us-east-1
	Default output format [None]: json

And if you think you screwed up that stuff, don’t panic. All of that info can be manually edited here:

    nano ~/.aws/config

Which has generic, non-credential config items like this:

	[default]
	region = us-east-1
	output = json

And in this file that stores credential-specific config items:

    nano ~/.aws/credentials

Which has credential info like this:

	[default]
	aws_access_key_id = ABCDEFGHIJKLMNOPQRST
	aws_secret_access_key = AbcdEfghiJklmnOpqrstUvwxyZ1234567890

#### Checking bucket info via Amazon Web Service CLI.

Get a list of all buckets:

    aws s3api list-buckets --output json

The output would be something like this:

	{
	    "Owner": {
	        "DisplayName": "some_username",
	        "ID": "1234567890abcdefghijklmnopqrstuvwxyz1234567890abcdefghijklmnopqr"
	    },
	    "Buckets": [
	        {
	            "CreationDate": "2014-12-13T06:59:17.000Z",
	            "Name": "my_bucket"
	        }
	    ]
	}

And here is a command to check a specific bucket’s info:

    aws s3api list-objects --bucket [your bucket name] --output json --query '[sum(Contents[].Size), length(Contents[])]'

Output returned would be something like this:

	[
	    0,
	    0
	]

***

*Amazon - Amazon Web Service Command Line Interface (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*