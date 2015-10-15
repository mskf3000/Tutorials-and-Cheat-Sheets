# Cheat Sheet - Java - Installing a Web Crawler based on Solr and Nutch

By Jack Szwergold, October 14, 2015

## Solr and Nutch web crawler prerequisites.

Solr and Nutch web crawler requires Java 6—or better—to be installed for it to work. So make sure the system you plan on running the Solr and Nutch web crawler on has Java 6 installed and `JAVA_HOME` is properly set on the system.

***

Make sure the following packages are installed on the Ubuntu setup:

	sudo aptitude install tomcat6 \
	                      ant ant-optional \
	                      unzip

Activate proxy related modules in Apache:

	sudo a2enmod proxy proxy_http

Now adjust some basics in the Tomcat6 config:

	sudo nano /etc/default/tomcat6

First, find the line for `JAVA_OPTS` and change as follows:

	# JAVA_OPTS="-Djava.awt.headless=true -Xmx128m -XX:+UseConcMarkSweepGC"
	JAVA_OPTS="-Xms128m -Xmx256m -XX:MaxPermSize=512m -Djava.awt.headless=true -XX:-UseGCOverheadLimit -XX:+UseCompressedOops"

Next, fine the area for `JAVA_HOME` and set it to the installed Java version like this:

	#JAVA_HOME=/usr/lib/jvm/openjdk-6-jdk
	JAVA_HOME=/usr/lib/jvm/java-6-oracle/jre

### Setting up Solr.

#### Installing the Solr binary.

Go into the `/usr/share` directory:

	cd /usr/share

Grab a compressed archive of `apache-solr-3.6.0.tgz` from an official Apache Solr source site:

	sudo curl -O -L http://archive.apache.org/dist/lucene/solr/3.6.0/apache-solr-3.6.0.tgz

Next, decompress the archive like this:

	sudo tar -xf apache-solr-3.6.0.tgz

Now adjust the ownership and group of the directory to `root` like this:

	sudo chown root:root -R /usr/share/apache-solr-3.6.0

#### Configuring Solr in Tomcat.

Create the Solr working directory:

	sudo mkdir -p /opt/solr

Now go into the main Solr directory in `/usr/share/`:

	cd /usr/share/apache-solr-3.6.0

Create a Tar archive of the `example/` directory:

	sudo tar -cf /opt/solr/example.tar example

Then go into the Solr working directory:

	cd /opt/solr

Decompress the `example.tar` archive:

	sudo tar -xf example.tar

And now copy the Solr `.war` file into the working directory:

	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-3.6.0.war /opt/solr/example/solr/solr.war

Create the `data/` directory in the Solr working directory:

    sudo mkdir -p /opt/solr/example/solr/data

Make sure `tomcat6` is the owner and group for all files/folders in '/opt/solr'

    sudo chown tomcat6:tomcat6 -R /opt/solr

Edit the Solr config file as follows:

    sudo nano /opt/solr/example/solr/conf/solrconfig.xml

Search for `solr.data.dir`. It should like this:

    <dataDir>${solr.data.dir:}</dataDir>

And change it like this to add in the `/opt/solr/example/solr/data` path:

    <dataDir>${solr.data.dir:/opt/solr/example/solr/data}</dataDir>

Create the context file for Solr in Tomcat

	sudo nano /var/lib/tomcat6/conf/Catalina/localhost/solr.xml
	
	<?xml version="1.0" encoding="utf-8"?>
	<Context docBase="/opt/solr/example/solr/solr.war" debug="0" crossContext="true">
	  <Environment name="solr/home" type="java.lang.String" value="/opt/solr/example/solr" override="true"/>
	</Context>

With that done, restart Tomcat like this:

    sudo service tomcat6 restart

And if all goes well, you can access the Solr web via the `solr/` URL path on your webserver like this:

    http://sandbox.local:8080/solr/

#### Solr debugging note.

To deal with an “undefined field text” message open `solrconfig.xml` and defaults in the Solr search handler.

    sudo nano /opt/solr/example/solr/conf/solrconfig.xml

Find the `requestHandler` config for `/select`:

	<requestHandler name="/select" class="solr.SearchHandler">
	  <!-- default values for query parameters can be specified, these
	       will be overridden by parameters in the request
	    -->
	   <lst name="defaults">
	     <str name="echoParams">explicit</str>
	     <int name="rows">10</int>
	     <str name="df">text</str>
	   </lst>

And change the `<str name="df">` to be `content` like this:

	<requestHandler name="/select" class="solr.SearchHandler">
	  <!-- default values for query parameters can be specified, these
	       will be overridden by parameters in the request
	    -->
	   <lst name="defaults">
	     <str name="echoParams">explicit</str>
	     <int name="rows">10</int>
	     <str name="df">content</str>
	     <!-- <str name="df">text</str> -->
	   </lst>

## Nutch related items.

### Installing Nutch.

#### Installing the Nutch binary.

Go into the `/usr/share` directory:

	cd /usr/share

Grab a compressed archive of `Apache-nutch-1.4-src.tar.gz` from an official Apache Nutch source site:

	sudo curl -O -L http://archive.apache.org/dist/nutch/apache-nutch-1.4-src.tar.gz

Next, decompress the archive like this:

	sudo tar -xf apache-nutch-1.4-src.tar.gz

Then adjust the ownership and group of the directory to `sysop` like this:

	sudo chown sysop:sysop -R /usr/share/apache-nutch-1.4

Now go into the main Nutch directory in `/usr/share/`:

	cd /usr/share/apache-nutch-1.4

#### Compiling Solr and Hadoop support for Nutch.

First, go back into your home directory like this:

    cd ~/

Grab a compressed archive of `hadoop-1.0.2.tar.gz` from an official Apache Hadoop source site:

	curl -O -L http://archive.apache.org/dist/hadoop/common/hadoop-1.0.2/hadoop-1.0.2.tar.gz
	
Next, decompress the archive like this:

	tar -xf hadoop-1.0.2.tar.gz

Now go into the main Hadoop `lib/native` directory like this:

	cd ~/hadoop-1.0.2/lib/native

Create a Tar archives of the `Linux-amd64-64` and `Linux-i386-32` directories like this:

	sudo tar -cf /usr/share/apache-nutch-1.4/lib/native/Linux-amd64-64.tar Linux-amd64-64
	sudo tar -cf /usr/share/apache-nutch-1.4/lib/native/Linux-i386-32.tar Linux-i386-32

Then go into the Nutch `lib/native` directory like this:

	cd /usr/share/apache-nutch-1.4/lib/native

And expand the `Linux-amd64-64` and `Linux-i386-32` Tar archives like this:

	sudo tar -xf Linux-amd64-64.tar
	sudo tar -xf Linux-i386-32.tar

Then copy the Solr specific libraries to the Nutch `lib/native` directory like this:

	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-solrj-3.6.0.jar /usr/share/apache-nutch-1.4/lib/apache-solr-solrj-3.6.0.jar
	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-core-3.6.0.jar /usr/share/apache-nutch-1.4/lib/apache-solr-core-3.6.0.jar

Then go into the main Nutch directory like this:

	cd /usr/share/apache-nutch-1.4

And compile via `ant`:

    ant

Wait and wait and after a while the compile should complete with a message something like this:

	BUILD SUCCESSFUL
	Total time: 2 minutes 32 seconds

### Configuring Nutch.

#### Setting up the core Nutch configuration.

First, copy the main Nutch default config file into a site specific file like this:

    sudo cp /usr/share/apache-nutch-1.4/runtime/local/conf/nutch-default.xml /usr/share/apache-nutch-1.4/runtime/local/conf/nutch-site.xml

And now use that Nutch XML config file for adjustments:

	sudo nano /usr/share/apache-nutch-1.4/runtime/local/conf/nutch-site.xml

Find the `parser.character.encoding.default` XML property:

	<name>parser.character.encoding.default</name>
	<value>windows-1252</value>

And change it to this:

	<name>parser.character.encoding.default</name>
	<!-- <value>windows-1252</value> -->
	<value>utf-8</value>

And adjust these user agent related property settings which identifies the crawler to the world:

	<name>http.agent.name</name>
	<value>PrewornBot</value>
	
	<name>http.robots.agents</name>
	<value>PrewornBot,*</value>
	
	<name>http.agent.description</name>
	<value>Preworn Web Crawler Bot - Version 1.4</value>
	
	<name>http.agent.url</name>
	<value>http://www.preworn.com/</value>
	
	<name>http.agent.email</name>
	<value>me@preworn.com</value>

If you feel like tweakig the fetcher for performance, adjust these XML properties for performance:

	<name>fetcher.threads.fetch</name>
	<name>fetcher.threads.per.host</name>
	<name>generate.max.count</name>
	<name>generate.count.mode</name>

#### Adjusting the Nutch regex stuff.

Open up the Nutch `regex-urlfilter.txt` file like this:

	nano /usr/share/apache-nutch-1.4/runtime/local/conf/regex-urlfilter.txt

Make sure that we do not accept anything else by commenting out this stuff:

	# accept anything else
	#+.
	
Add the regex patterns that match the domains and hosts you’d like indexed.

	# allow urls in preworn.com domain
	+^http://([a-z0-9\-A-Z]*\.)*preworn.com/([a-z0-9\-A-Z]*\/)*

Then finally add this to the bottom of the file to deny anything else:

	# deny anything else
	-.

#### Adding Seed URLs to Nutch.

Creat the `urls/` directory like this:

	mkdir -p /usr/share/apache-nutch-1.4/runtime/local/urls

And now create the `seed.txt` file like this:

	nano /usr/share/apache-nutch-1.4/runtime/local/urls/seed.txt

And just ad whatever URLs you wish have crawled to that file. Remember, these are seed URLs so in many cases you can just add the main domain for the site like `http://www.preworn.com` and the system will automatically harvest the child URLs from that see URL.

### Configuring Solr based on the Nutch schema.

Back up the original file:

	sudo mv /opt/solr/example/solr/conf/schema.xml /opt/solr/example/solr/conf/schema.xml.orig

And replace the default Solr `schema.xml` with the Solr `schema.xml` provided by Nutch:

	sudo cp /usr/share/apache-nutch-1.4/runtime/local/conf/schema.xml /opt/solr/example/solr/conf/schema.xml

Now edit the Solr `schema.xml`:

	sudo nano /opt/solr/example/solr/conf/schema.xml

And change the following line regarding the `content` field from `false`:

	<field name="content" type="text" stored="false" indexed="true"/>

To be `true` like this:

	<field name="content" type="text" stored="true" indexed="true"/>

With that done, make a copy of the `solrconfig.xml` for backup:

	sudo cp /opt/solr/example/solr/conf/solrconfig.xml /opt/solr/example/solr/conf/solrconfig.xml.orig

Now edit the `solrconfig.xml` :

    sudo nano /opt/solr/example/solr/conf/solrconfig.xml

And add a request handler for `/nutch`; place it before the closing `</config>` tag:

	<!-- Request handler for nutch -->
	<requestHandler name="/nutch" class="solr.SearchHandler" >
	  <lst name="defaults">
	    <str name="defType">dismax</str>
	    <str name="echoParams">explicit</str>
	    <str name="tie">0.01</str>
	    <str name="qf">
	      content^0.5 anchor^1.0 title^1.2
	    </str>
	    <str name="pf">
	      content^0.5 anchor^1.5 title^1.2 site^1.5
	    </str>
	    <str name="fl">
	      url
	    </str>
	    <str name="mm">
	      2&lt;-1 5&lt;-2 6&lt;90%
	    </str>
	    <str name="ps">100</str>
	    <str name="hl">true</str>
	    <str name="q.alt">*:*</str>
	    <str name="hl.fl">title url content</str>
	    <str name="f.title.hl.fragsize">0</str>
	    <str name="f.title.hl.alternateField">title</str>
	    <str name="f.url.hl.fragsize">0</str>
	    <str name="f.url.hl.alternateField">url</str>
	    <str name="f.content.hl.fragmenter">regex</str>
	  </lst>
	</requestHandler>

With all that done, restart Tomcat:

    sudo service tomcat6 restart

### Install the `index-metatags` plug-in for Nutch and Solr.

	curl -O -L https://issues.apache.org/jira/secure/attachment/12510323/metatags-plugin%2Btutorial.zip

	unzip metatags-plugin+tutorial

	unzip index-metatags

	mv ~/index-metatags /usr/share/apache-nutch-1.4/runtime/local/plugins/index-metatags

	sudo nano /usr/share/apache-nutch-1.4/runtime/local/conf/nutch-site.xml

Adjust this property so the metatags plugin is loaded.

	<property>
	  <name>plugin.includes</name>
	  <!-- <value>protocol-http|urlfilter-regex|parse-(html|tika|js|zip)|index-(basic|anchor|metatags)|query-(basic|site|url)|response-(json|xml)|summary-basic|scoring-opic|urlnormalizer-(pass|regex|basic)</value> -->
	  <value>protocol-http|urlfilter-regex|parse-(html|tika)|index-(basic|anchor|metatags|more)|query-(basic|site|url)|response-(json|xml)|summary-basic|scoring-opic|urlnormalizer-(pass|regex|basic)</value>
	</property>

Configuration for the metatags plugin:

	<!-- Used only if plugin parse-metatags is enabled. -->
	<property>
	  <name>metatags.names</name>
	  <value>description;keywords</value>
	  <description>For plugin parse-metatags: Indicate here the name of the
	  html meta tag that should be parsed. Use a semicolon separated list
	  if you want multiple tags, or use '*' to index all.
	  Example: description;keywords;role
	  </description>
	</property>

Add fields to be indexed to the Solr schema:

    sudo nano /opt/solr/example/solr/conf/schema.xml

	<!-- fields for metatags plugin -->
	<field name="description" type="string" stored="true" indexed="true"/>
	<field name="keywords" type="string" stored="true" indexed="true"/>

### Setting up an Apache reverse proxy for Tomcat.

#### Restrict Tomcat6 to `localhost` (`127.0.0.1`) only.

If you have tested your setup and it works, you might want to restrict the Solr setup to only be accessible via the `localhost`

Open the main Tomcat6 server config file:

    sudo nano /etc/tomcat6/server.xml

Search for the following:

	<Connector port="8080" protocol="HTTP/1.1"
	           connectionTimeout="20000"
	           URIEncoding="UTF-8"
	           redirectPort="8443" />

And change it to this; note the `address="127.0.0.1"` line:

	<Connector port="8080" protocol="HTTP/1.1"
	           address="127.0.0.1"
	           connectionTimeout="20000"
	           URIEncoding="UTF-8"
	           redirectPort="8443" />

#### Adjust the Apache2 config to allow the Solr server to be accessed via a reverse proxy.

	# Settings for adding a trailing slash to the URL
	RewriteEngine On
	RewriteCond %{REQUEST_URI} ^/(solr)$
	RewriteRule ^(.*)$ http://%{HTTP_HOST}$1/ [R=301,L]
	
	# Settings for Solr
	<IfModule mod_proxy.c>
	
	  # Proxy specific settings
	  ProxyRequests Off
	  ProxyPreserveHost On
	
	  <Proxy *>
	    AddDefaultCharset off
	    Order deny,allow
	    Allow from all
	  </Proxy>
	
	  ProxyPass /solr http://localhost:8080/solr/
	  ProxyPassReverse /solr http://localhost:8080/solr/
	
	  <Location /solr>
	    AuthName "Preworn DEV Server"
	    AuthType Basic
	    require valid-user
	    AuthUserFile /etc/apache2/passwords
	
	    Order Allow,Deny
	    Satisfy Any
	    Allow from 127.0.0.1 ::1
	    # Allow from localhost
	  </Location>
	
	</IfModule>

***

*Cheat Sheet - Java - Installing a Web Crawler based on Solr and Nutch (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
