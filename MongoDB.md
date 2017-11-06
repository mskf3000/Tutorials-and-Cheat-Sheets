## MongoDB

By Jack Szwergold

### Install MongoDB.

First, import the public key used by the package management system:

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10

Next, create a sources list file for MongoDB:

    echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list

Now, run `aptitude update` like this:

    sudo aptitude update

Then install MongoDB via `aptitude` like this:

    sudo aptitude install mongodb-org

Or install a specific release of MongoDB via `aptitude` like this:

    sudo aptitude install mongodb-org=2.6.12 mongodb-org-server=2.6.12 mongodb-org-shell=2.6.12 mongodb-org-mongos=2.6.12 mongodb-org-tools=2.6.12

Check the version number like this:

    mongod --version

And the output should be something like this:

	db version v2.6.12
	2016-09-13T10:39:01.986-0400 git version: d73c92b1c85703828b55c2916a5dd4ad46535f6a

And the output should be something like this:

	db version v2.6.12

Get logging level information like this:

	db.adminCommand({getParameter: 1, logLevel: 1});

Get logging profiling status info like this:

	db.getProfilingStatus();

Get Mongo server status:

	db.serverStatus();

Get Mongo command line options:

	db.serverCmdLineOpts();

Set the Mongo profiling level to 2 and slow milliseconds to 0 to log all queries:

	db.setProfilingLevel(2, 0);

#### Pin a specific version of MongoDB.

Although you can specify any available version of MongoDB, `aptitude` will upgrade the packages when a newer version becomes available. To prevent unintended upgrades, pin the package. To pin the version of MongoDB at the currently installed version, issue the following command sequence:

	echo "mongodb-org hold" | sudo dpkg --set-selections
	echo "mongodb-org-server hold" | sudo dpkg --set-selections
	echo "mongodb-org-shell hold" | sudo dpkg --set-selections
	echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
	echo "mongodb-org-tools hold" | sudo dpkg --set-selections

#### Sundry MongoDB items on an Ubuntu/Debian system.

Start, stop and control MongoDB on an Ubuntu/Debian system:

	sudo service mongod status
	sudo service mongod start
	sudo service mongod stop
	sudo service mongod restart
	sudo service mongod reload
	sudo service mongod force-reload

Edit the MongoDB configuration file:

    sudo nano /etc/mongod.conf

MongoDB configuration storage location:

    ls -lah /var/lib/mongodb

Follow the MongoDB logs:

    tail -f -n 200 /var/log/mongodb/mongod.log
    tail -f -n 200 /var/log/mongodb/mongodb.log

#### Bind to `127.0.0.1` to enable networking.

To enable networking—so someone other that `localhost` can connect to the setup—open up the MongoDB config on the server.

    sudo nano /etc/mongod.conf

Find this line:

	# Listen to local interface only. Comment out to listen on all interfaces.
	bind_ip = 127.0.0.1

And comment out the `bind_ip` like this:

	# Listen to local interface only. Comment out to listen on all interfaces.
	#bind_ip = 127.0.0.1

Now restart MongDB and networking should be enabled:

    sudo service mongod restart

You can confirm this by checking if port `27017` is open on the local host via `nmap` like this:

    nmap sandbox.local -p27017

The positive response should be something like this:

	Starting Nmap 6.47 ( http://nmap.org ) at 2015-09-22 00:53 EDT
	Nmap scan report for sandbox.local (192.168.56.10)
	Host is up (0.00043s latency).
	PORT      STATE SERVICE
	27017/tcp open  unknown

### Practical MongoDB usage examples.

#### Example MongoDB URL format.

An example of a MongoDB connection URL format:

    mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]

#### Connect to a MongoDB instance.

Just type `mongo` into the command line to get into the interface:

    mongo

By default, `mongo` looks for a database server listening on port `27017` on the `localhost` interface. To connect to a server on a different port or interface, use the `--port` and `--host` options. Like this example that allows you to connect to a remote MongoDB server:

    mongo [hosname or IP address]:[port number]/[dbname] -u [username] -p [password]

Report the name of the current database:

    db

Show databases:

    show dbs

Use a specific database:

    use mydb

Get stats on a specific database you are using:

    db.stats()

#### MongoDB user management.

Type in `mongo` to login to the database:

    mongo

You can now do whatever you want thanks to the `localhost` exception for clean installs:

> The `localhost` exception allows you to enable authorization before creating the first user in the system. When active, the `localhost` exception allows all connections from the `localhost` interface to have full access to that instance. The exception applies only when there are no users created in the MongoDB instance.


Check the user privleges for the user called `root`:

	db.runCommand(
	  {
	    usersInfo:"root",
	    showPrivileges:true
	  }
	)

If the results are empty, them let’s create a user called `siteUserAdmin`. First connect to `admin` like this:

    use admin

And use `createUser` like this:

	db.createUser(
	  {
	    user: "root",
	    pwd: "root",
	    roles:
	    [
	      {
	        role: "userAdminAnyDatabase",
	        db: "admin"
	      }
	    ]
	  }
	)

Now check the `root` user again:

	db.runCommand(
	  {
	    usersInfo:"root",
	    showPrivileges:true
	  }
	)

And if you want to change the password after the fact, run a command like this:

    db.changeUserPassword("root", "root")

Check to see if you have any user in the database with admin rights like this:

    db.getSiblingDB('admin').system.users.find()

Output should be something like this:

	{ "_id" : "admin.siteUserAdmin", "user" : "siteUserAdmin", "db" : "admin", "credentials" : { "MONGODB-CR" : "5a526e5d4d622d01a7feae8b7e5f470d" }, "roles" : [ { "role" : "userAdminAnyDatabase", "db" : "admin" } ] }

Check the mongo command line options by running this command:

    db.serverCmdLineOpts()

To have `localhost` login exceptions disabled one of the following should be set to `true`:

	db.serverCmdLineOpts().parsed.auth
	db.serverCmdLineOpts().parsed.keyFile

To reenable authorization open up `/etc/mongod.conf`:

    sudo nano /etc/mongod.conf

Find these lines around line 28:

	# Turn on/off security.  Off is currently the default
	#noauth = true
	#auth = true

Set that `auth` to `true` like this:

	# Turn on/off security.  Off is currently the default
	#noauth = true
	auth = true

Then restart MongoDB and all should be good:

	sudo service mongod restart

Now login to MongoDB like this:

    mongo --port 27017 -u root -p root --authenticationDatabase admin

#### Revoke a user role in MongoDB.

First connect to `admin` like this:

    use admin

And use `db.revokeRolesFromUser` like this:

	db.revokeRolesFromUser(
	    "root",
	    [
	      {
	        role: "userAdminAnyDatabase",
	        db: "admin"
	      }
	    ]
	)

Now check the `root` user again:

	db.runCommand(
	  {
	    usersInfo:"root",
	    showPrivileges:true
	  }
	)

#### Assign a user role in MongoDB.

First connect to `admin` like this:

    use admin

And use `db.grantRolesToUser` like this:

	db.grantRolesToUser(
	    "root",
	    [
	      {
	        role: "root",
	        db: "admin"
	      }
	    ]
	)

Now check the `root` user again:

	db.runCommand(
	  {
	    usersInfo:"root",
	    showPrivileges:true
	  }
	)

#### Create a MongoDB database and use it.

Creating a database is as simple as this:

    use sandbox_dev

But when you run `show dbs` the database won’t be there:

    admin  0.078GB
    local  0.078GB

At least one actual record should be inserted for the database to actually exist. Here is test/example data:

    db.artist.insert({"name":"hello world"})

Now when you run `show dbs` the database will be there:

	admin             0.078GB
	sandbox_dev       0.078GB
	local             0.078GB

### Some MongoDB debugging and maintenance items.

#### Compacting a MongoDB database.

Compacting a specific collection in a database to reclaim used space:

    db.runCommand({ compact: 'sessions', force: true });

Compacting all collections in a database to save space:

	db.getCollectionNames().forEach(function (collectionName) {
	    print('Compacting: ' + collectionName);
	    db.runCommand({ compact: collectionName });
	});

#### Exporting, droppping and restoring a database.

Export a database:

    mongodump -d databasename

Drop a database:

    echo 'db.dropDatabase()' | mongo [database name];

Or use this command to drop a database:

    mongo "[database name]" --eval "db.dropDatabase()";

Add the `--quiet` option to suppress output:

    mongo --quiet "[database name]" --eval "db.dropDatabase()";

Restore a database:

    mongorestore dump/databasename

#### Disable journaling for cases when memory limit errors pop up.

The error would look something like this:

    ERROR: mmap private failed with out of memory. (64 bit build)

Open up the MongoDB config here:

	sudo nano /etc/mongod.conf

Look for this line:
	
	journal = true

And change it to this; using `nojournal` instead of `journal`:
	
	nojournal = true

#### Delete the lock file if the system has crashed.

If the error log has a line like this in it:

    [initandlisten] exception in initAndListen: 12596 old lock file, terminating

Then do this to get rid of the old lock file:

    sudo rm  /var/lib/mongodb/mongod.lock

***

*MongoDB (c) by Jack Szwergold; written on September 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*