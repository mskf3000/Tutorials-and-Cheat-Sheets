# Cheat Sheet - Elasticsearch

By Jack Szwergold, September 21, 2015

## Elasticsearch prerequisites.

Elasticsearch requires Java 7—or better—to be installed for it to work. So make sure the system you plan on running Elasticsearch on has Java 7 installed and `JAVA_HOME` is properly set on the system.

## Install Elasticsearch.

First, import the public key used by the package management system:

    wget -qO - http://packages.elasticsearch.org/GPG-KEY-elasticsearch | sudo apt-key add -

Next, create a sources list file for Elasticsearch:

    echo 'deb http://packages.elasticsearch.org/elasticsearch/1.4/debian stable main' | sudo tee /etc/apt/sources.list.d/elasticsearch.list

Now, run `aptitude update` like this:

    sudo aptitude update

Then install Elasticsearch via `aptitude` like this:

    sudo aptitude install elasticsearch

Finally Elasticsearch will be installed, but it won’t be set to startup on boot. So run this `update-rc.d` command to get that set:

    sudo update-rc.d elasticsearch defaults 95 10

If somehow you need to remove the `update-rc.d` stuff, remove these items:

    sudo rm /etc/rc0.d/K10elasticsearch
    sudo rm /etc/rc1.d/K10elasticsearch
    sudo rm /etc/rc6.d/K10elasticsearch
    sudo rm /etc/rc2.d/S95elasticsearch
    sudo rm /etc/rc3.d/S95elasticsearch
    sudo rm /etc/rc4.d/S95elasticsearch
    sudo rm /etc/rc5.d/S95elasticsearch

#### Sundry Elasticsearch items on an Ubuntu/Debian system.

Start, stop and control Elasticsearch on an Ubuntu/Debian system:

	sudo service elasticsearch start
	sudo service elasticsearch stop
	sudo service elasticsearch restart
	sudo service elasticsearch force-reload
	sudo service elasticsearch status

Edit the Elasticsearch configuration file:

    sudo nano /etc/default/elasticsearch

Elasticsearch configuration storage location.

    ls -la /etc/elasticsearch

Elasticsearch log storage location:

    ls -la /var/log/elasticsearch

Another Elasticsearch configuration file:

    sudo nano /etc/elasticsearch/elasticsearch.yml

Follow the main Elasticsearch log:

    tail -f -n 200 /var/log/elasticsearch/elasticsearch.log

Follow the main Elasticsearch “slow” log:

    tail -f -n 200 /var/log/elasticsearch/elasticsearch_index_indexing_slowlog.log
    tail -f -n 200 /var/log/elasticsearch/elasticsearch_index_search_slowlog.log

Elasticsearch data storage location:

    ls -la /var/lib/elasticsearch

Elasticsearch work file storage location:

    ls -la /tmp/elasticsearch

#### Testing Elasticsearch.

With Elasticsearch installed—and with the service script installed—we can start it by running this command:

    sudo service elasticsearch start

It should start up immediately and return the following:

    * Starting Elasticsearch Server

And can then be accessed via port `9200` like this:

    http://sandbox.local:9200

You can check returned headers like this:

    curl -I http://sandbox.local:9200

If running, it will cleanly return a `200` status like this:

	HTTP/1.1 200 OK
	Content-Type: text/plain; charset=UTF-8
	Content-Length: 0

And will send back data via the command line using `curl` as well:

    curl http://sandbox.local:9200

The response should be something like:

	{
	  "status" : 200,
	  "name" : "Watcher",
	  "cluster_name" : "elasticsearch",
	  "version" : {
	    "number" : "1.4.5",
	    "build_hash" : "2aaf797f2a571dcb779a3b61180afe8390ab61f9",
	    "build_timestamp" : "2015-04-27T08:06:06Z",
	    "build_snapshot" : false,
	    "lucene_version" : "4.10.4"
	  },
	  "tagline" : "You Know, for Search"
	}

#### Bind to `localhost` to disable networking.

By default, Elasticsearch will be networked to allow connections from anywhere, everywhere. If that’s not really desired you can disable networking like this.

First, open up the main config file here:

    sudo nano /etc/elasticsearch/elasticsearch.yml

Find the `network.host` config area:

	# Set both 'bind_host' and 'publish_host':
	#
	#network.host: 192.168.0.1

And adjust it like so:

	# Set both 'bind_host' and 'publish_host':
	#
	#network.host: 192.168.0.1
	network.host: localhost

Then restart Elasticsearch:

    sudo service elasticsearch restart

And it will now only be available to `localhost` connections on port `9200` moving forward so it would have to be accessed on the server directly like this:

    curl http://localhost:9200

## Practical Elasticsearch data usage examples.

#### Create an index.

This creates the “pranks” index:

    curl -XPUT 'http://sandbox.local:9200/pranks?pretty=true'

This creates the “jokes” index:

    curl -XPUT 'http://sandbox.local:9200/jokes?pretty=true'

#### Enter test data.

With the index created, let’s add some test data to the “pranks” index:

    curl -XPUT 'http://sandbox.local:9200/pranks/tubebar/1?pretty=true' -d '{ "name" : "Al Coholic" }'

Which should respond with something like this:

	{
	  "_index" : "pranks",
	  "_type" : "tubebar",
	  "_id" : "1",
	  "_version" : 1,
	  "created" : true
	}

And now some more test data to the “jokes” index:

    curl -XPUT 'http://sandbox.local:9200/jokes/bagels/1?pretty=true' -d '{ "question" : "How do you keep a bagel safe?", "answer" : "Put lox on it!" }'

Which should respond with something like this:

	{
	  "_index" : "jokes",
	  "_type" : "bagels",
	  "_id" : "1",
	  "_version" : 1,
	  "created" : true
	}

Verify the data was entered:

    curl -XGET 'http://sandbox.local:9200/pranks/tubebar/1?pretty=true'

    curl -XGET 'http://sandbox.local:9200/jokes/bagels/1?pretty=true'

The verification should be returned like this:

	{
	  "_index" : "pranks",
	  "_type" : "tubebar",
	  "_id" : "1",
	  "_version" : 1,
	  "found" : true,
	  "_source":{ "name" : "Al Coholic" }
	}

	{
	  "_index" : "jokes",
	  "_type" : "bagels",
	  "_id" : "1",
	  "_version" : 1,
	  "found" : true,
	  "_source":{ "question" : "How do you keep a bagel safe?", "answer" : "Put lox on it!" }
	}

#### Search the data.

Let’s do a search like this:

    curl 'http://sandbox.local:9200/pranks/tubebar/_search?q=name:al&pretty=true'

And the returned data should be:

	{
	  "took" : 86,
	  "timed_out" : false,
	  "_shards" : {
	    "total" : 5,
	    "successful" : 5,
	    "failed" : 0
	  },
	  "hits" : {
	    "total" : 1,
	    "max_score" : 0.19178301,
	    "hits" : [ {
	      "_index" : "pranks",
	      "_type" : "tubebar",
	      "_id" : "1",
	      "_score" : 0.19178301,
	      "_source":{ "name" : "Al Coholic" }
	    } ]
	  }
	}

And let’s do a search like this:

    curl 'http://sandbox.local:9200/jokes/bagels/_search?q=answer:lox&pretty=true'

And the returned data should be:

	{
	  "took" : 6,
	  "timed_out" : false,
	  "_shards" : {
	    "total" : 5,
	    "successful" : 5,
	    "failed" : 0
	  },
	  "hits" : {
	    "total" : 1,
	    "max_score" : 0.15342641,
	    "hits" : [ {
	      "_index" : "jokes",
	      "_type" : "bagels",
	      "_id" : "1",
	      "_score" : 0.15342641,
	      "_source":{ "question" : "How do you keep a bagel safe?", "answer" : "Put lox on it!" }
	    } ]
	  }
	}

#### List all indexes.

This lists all indexes:

    curl 'http://sandbox.local:9200/_cat/indices?v'

#### Flush data.

Flush all data to rebuild:

    curl -XPOST 'http://sandbox.local:9200/_flush?pretty=true'

Flush a specific named index:

    curl -XPOST 'http://sandbox.local:9200/pranks/_flush?pretty=true'

Flush specific named indexes:

    curl -XPOST 'http://sandbox.local:9200/pranks,jokes/_flush?pretty=true'

#### Delete indexes.

This will delete a named index:

    curl -XDELETE 'http://sandbox.local:9200/pranks/?pretty=true'

This will delete all indexes:

    curl -XDELETE 'http://sandbox.local:9200/_all?pretty=true'

This will also delete all indexes:

    curl -XDELETE 'http://sandbox.local:9200/*?pretty=true'

***

*Cheat Sheet - Elasticsearch (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
