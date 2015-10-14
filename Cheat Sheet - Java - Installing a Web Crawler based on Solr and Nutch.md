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

Adjust memory usage in Tomcat:

	sudo nano /etc/default/tomcat6

Find the line for `JAVA_OPTS` and change as follows:

	# JAVA_OPTS="-Djava.awt.headless=true -Xmx128m -XX:+UseConcMarkSweepGC"
	JAVA_OPTS="-Xms128m -Xmx256m -XX:MaxPermSize=512m -Djava.awt.headless=true -XX:-UseGCOverheadLimit -XX:+UseCompressedOops"

### Setting up Solr binary.

#### Setting up Solr in Tomcat.

Go into the `/usr/share` directory:

	cd /usr/share

And grab a compressed archive of `apache-solr-3.6.0.tgz` from an official Apache Solr source site:

	sudo curl -O -L http://archive.apache.org/dist/lucene/solr/3.6.0/apache-solr-3.6.0.tgz

Next, decompress the archive like this:

	sudo tar -xf apache-solr-3.6.0.tgz

Now adjust the ownership to the directory like this:

	sudo chown root:root -R /usr/share/apache-solr-3.6.0

#### Setting up Solr in Tomcat.

Setting up Solr in Tomcat.

	sudo mkdir /opt/solr
	cd /usr/share/apache-solr-3.6.0
	sudo tar -cf /opt/solr/example.tar example
	cd /opt/solr
	sudo tar -xf example.tar
	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-3.6.0.war /opt/solr/example/solr/solr.war

Create the 'data' directory in Solr.

    sudo mkdir /opt/solr/example/solr/data

Make sure `tomcat6` is the owner and group for all files/folders in '/opt/solr'

    sudo chown tomcat6:tomcat6 -R /opt/solr

Edit the Solr config file as follows.

    sudo nano /opt/solr/example/solr/conf/solrconfig.xml

Search for `solr.data.dir` and change it to this.

    <dataDir>${solr.data.dir:/opt/solr/example/solr/data}</dataDir>

***

For "undefined field text" open `solrconfig.xml` and defaults in the Solr search handler.

    sudo nano /opt/solr/example/solr/conf/solrconfig.xml

	<requestHandler name="/select" class="solr.SearchHandler">
	  <!-- default values for query parameters can be specified, these
	       will be overridden by parameters in the request
	    -->
	   <lst name="defaults">
	     <str name="echoParams">explicit</str>
	     <int name="rows">10</int>
	     <str name="df">content</str>
	     <!-- <str name="df">text</str> -->

***

Create the context file for Solr in Tomcat

	sudo nano /var/lib/tomcat6/conf/Catalina/localhost/solr.xml
	
	<?xml version="1.0" encoding="utf-8"?>
	<Context docBase="/opt/solr/example/solr/solr.war" debug="0" crossContext="true">
	  <Environment name="solr/home" type="java.lang.String" value="/opt/solr/example/solr" override="true"/>
	</Context>

### Compile Nutch 1.4 from source.

	cd /usr/share
	sudo wget http://archive.apache.org/dist/nutch/apache-nutch-1.4-src.tar.gz
	sudo tar -xf apache-nutch-1.4-src.tar.gz
	sudo chown searchbot:searchbot -R /usr/share/apache-nutch-1.4
	cd /usr/share/apache-nutch-1.4

Copy the Solr specific libraries to Nutch for a clean and compatible compile.

	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-solrj-3.6.0.jar /usr/share/apache-nutch-1.4/lib/apache-solr-solrj-3.6.0.jar
	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-core-3.6.0.jar /usr/share/apache-nutch-1.4/lib/apache-solr-core-3.6.0.jar

Now get the Hadoop libraries and copy/compile for native Hadoop support in Nutch.

	sudo wget http://mirror.uoregon.edu/apache/hadoop/common/hadoop-1.0.2/hadoop-1.0.2.tar.gz
	
	sudo tar -xf hadoop-1.0.2.tar.gz
	cd ~/hadoop-1.0.2/lib/native
	
	sudo tar -cf /usr/share/apache-nutch-1.4/lib/native/Linux-amd64-64.tar Linux-amd64-64
	sudo tar -cf /usr/share/apache-nutch-1.4/lib/native/Linux-i386-32.tar Linux-i386-32
	
	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-solrj-3.6.0.jar /usr/share/apache-nutch-1.4/lib/apache-solr-solrj-3.6.0.jar
	sudo cp /usr/share/apache-solr-3.6.0/dist/apache-solr-core-3.6.0.jar /usr/share/apache-nutch-1.4/lib/apache-solr-core-3.6.0.jar
	
	cd /usr/share/apache-nutch-1.4/lib/native
	
	sudo tar -xf Linux-amd64-64.tar
	sudo tar -xf Linux-i386-32.tar

Compile via `ant`.

    ant

### Adjust Nutch Settings

	sudo nano /usr/share/apache-nutch-1.4/runtime/local/conf/nutch-site.xml

Adjust for performance.

	<name>fetcher.threads.fetch</name>
	<name>fetcher.threads.per.host</name>
	<name>generate.max.count</name>
	<name>generate.count.mode</name>

Change from this…

	<name>parser.character.encoding.default</name>
	<value>windows-1252</value>

To this…

	<name>parser.character.encoding.default</name>
	<value>utf-8</value>

Change the settings that identify the crawler to the world.

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

### Adjust Nutch Regex

	sudo nano /usr/share/apache-nutch-1.4/runtime/local/conf/regex-urlfilter.txt

Add the regex patterns that match the domains and hosts you’d like indexed.

	# allow urls in preworn.com domain
	+^http://([a-z0-9\-A-Z]*\.)*preworn.com/([a-z0-9\-A-Z]*\/)*

This should be added just before the following regex that denies anything else.

	# deny anything else
	-.

Also, make sure that we do not accept anything else by commenting out this line.

	# accept anything else
	#+.

### Adding Seed URLs to Nutch

	mkdir /usr/share/apache-nutch-1.4/runtime/local/urls
	
	nano /usr/share/apache-nutch-1.4/runtime/local/urls/seed.txt
	[add URLs]

### Configuring Solr based on the Nutch schema.

Back up the original file:

	sudo mv /opt/solr/example/solr/conf/schema.xml /opt/solr/example/solr/conf/schema.xml.orig

And replace the Solr schema with the one provided by Nutch:

	sudo cp /usr/share/apache-nutch-1.4/runtime/local/conf/schema.xml /opt/solr/example/solr/conf/schema.xml

Now edit the Solr `schema.xml`:

	sudo nano /opt/solr/example/solr/conf/schema.xml

And change the following line regarding the `content` field from `false`…

	<field name="content" type="text" stored="false" indexed="true"/>

To `true`…

	<field name="content" type="text" stored="true" indexed="true"/>

Make a copy of the `solrconfig.xml` for backup.

	sudo cp /opt/solr/example/solr/conf/solrconfig.xml /opt/solr/example/solr/conf/solrconfig.xml.orig

Now edit the `solrconfig.xml` to add a `/nutch` search handler.

    sudo nano /opt/solr/example/solr/conf/solrconfig.xml

Add the request handler for `/nutch`.

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

Restart Tomcat.

    sudo service tomcat6 restart

### Install the `index-metatags` plug-in for Nutch and Solr.

	wget https://issues.apache.org/jira/secure/attachment/12510323/metatags-plugin%2Btutorial.zip
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

Configuration for the metatags plugin.

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

Add fields to be indexed to the Solr schema.

    sudo nano /opt/solr/example/solr/conf/schema.xml

	<!-- fields for metatags plugin -->
	<field name="description" type="string" stored="true" indexed="true"/>
	<field name="keywords" type="string" stored="true" indexed="true"/>

### Test the above stuff out and if it works, restrict Tomcat6 to localhost (127.0.0.1) only.

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

### Adjust the Apache2 config to allow the Solr server to be accessed via a reverse proxy.

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

### Adjust the `robots.txt` file so the crawler can get better access and speed.

	User-agent: PrewornBot
	Crawl-delay: 1

***

### How to purge the Nutch crawl data.

	sudo rm -rf /usr/share/apache-nutch-1.4/runtime/local/crawl/crawldb/current/*
	sudo rm -rf /usr/share/apache-nutch-1.4/runtime/local/crawl/segments/*

### Delete the Solr index.

	curl http://localhost:8080/solr/update?commit=true -H "Content-Type: text/xml" --data-binary '<delete><query>*:*</query></delete>'

### Optimize the Solr database.

If you check “Solr Statistics” and `maxDoc` is higher than `numDocs`, that means an optimization needs to take place.

	curl http://localhost:8080/solr/update?optimize=true

***

### How to follow log files.

	sudo tail -f -n 200 /var/log/tomcat6/solr.2012-05-08.log
	
	sudo tail -f -n 200 /usr/share/apache-nutch-1.4/runtime/local/logs/hadoop.log
	
	sudo tail -f -n 200 /var/log/tomcat6/catalina.2012-08-15.log
	
	sudo tail -f -n 200 /var/log/tomcat6/catalina.out

***

*Cheat Sheet - Java - Installing a Web Crawler based on Solr and Nutch (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
