# Cheat Sheet - Java

By Jack Szwergold, September 21, 2015

### Installing Java via the WebUpd8 repository.

#### Adding the WebUpd8 PPA to the system.

First install `python-software-properties` like this:

	sudo aptitude install python-software-properties

Next add the WebUpd8 PPA repository to the system like this:

	sudo add-apt-repository ppa:webupd8team/java

With that done, run `aptitude update` to get the new repository picked up like this:

	sudo aptitude update

### Install various versions of Java via the WebUpd8 PPA.

#### Install Java 6.

Now install Java (version 6) via `aptitude` like this:

    sudo aptitude install oracle-java6-installer

Check the version number like this:

    java -version

And the output should be something like this:

	Java(TM) SE Runtime Environment (build 1.6.0_45-b06)
	Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode)

#### Install Java 7.

Now install Java (version 7) via `aptitude` like this:

    sudo aptitude install oracle-java7-installer

Check the version number like this:

    java -version

And the output should be something like this:

	java version "1.7.0_80"
	Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
	Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)

#### Install Java 8.

Now install Java (version 8) via `aptitude` like this:

    sudo aptitude install oracle-java8-installer

Check the version number like this:

    java -version

And the output should be something like this:


### Sundry Java items.

#### Add a `JAVA_HOME` value to the system `/etc/environment` file.

Add or adjust this line to `/etc/environment` to match your newly installed Java:

    sudo nano /etc/environment

	JAVA_HOME=/usr/lib/jvm/java-6-oracle/jre
	
	JAVA_HOME=/usr/lib/jvm/java-7-oracle/jre
	
	JAVA_HOME=/usr/lib/jvm/java-8-oracle/jre

#### Detect the version of Java a class was compiled under

    javap -verbose MyClass | more

You want the major version from the results. Here are some example values:

	Java 1.2 uses major version 46
	Java 1.3 uses major version 47
	Java 1.4 uses major version 48
	Java 5 uses major version 49
	Java 6 uses major version 50
	Java 7 uses major version 51

### Removing older Java items.

#### Get rid of the default “partner” repository installed version of Java.

	sudo aptitude purge java-common libcommons-collections3-java libcommons-dbcp-java libcommons-pool-java libecj-java libservlet2.5-java libtomcat6-java oracle-java6-installer sun-java6-bin sun-java6-jre

#### Removing the `oab-java.sh` Java install items.

If you somehow installed Java prviously via the `oab-java.sh` script, you can get rid of that stuff like this.

First, check what is in the `sources.list` directory:

	ls -la /etc/apt/sources.list.d/

Next, get rid of the previously installed `oab-java.sh` Java packages:

	sudo aptitude purge sun-java6-jdk sun-java6-fonts sun-java6-source

Now, remove the `oab-java.sh` PPA (Personal Package Archive) stuff:

    sudo rm /etc/apt/sources.list.d/oab.list

Finally, remove the old `oab-java.sh` items from `/var/local/`.

***

*Cheat Sheet - Java (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*


