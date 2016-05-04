## Java - Using a Web Crawler based on Solr and Nutch

By Jack Szwergold, October 14, 2015

### Crawling via Bash script.

Just run `crawl_and_index.sh` to get the crawler to run automatically:

    nohup /opt/crawl_and_index.sh &

### Crawling with Nutch.

Or do it step-by-step manually:

    cd /usr/share/apache-nutch-1.4/runtime/local

Start by injecting the seed url(s) to the Nutch crawl database (`crawldb`):

    bin/nutch inject crawl/crawldb urls

Now generate a fetch list based on the seed URLs:

    bin/nutch generate crawl/crawldb crawl/segments

The above command generated a new segment directory under crawl/segments that contains the urls to be fetched. All following commands require accessing the latest segment directory as their main parameter so we’ll store it in an environment variable:

    export SEGMENT=crawl/segments/$(ls -tr crawl/segments | tail -1)

Launch the crawler!

    bin/nutch fetch $SEGMENT -noParsing

And parse the fetched content:

    bin/nutch parse $SEGMENT

Now we need to update the crawl database to ensure that for all future crawls, Nutch only checks the already crawled pages, and only fetches new and changed pages.

    bin/nutch updatedb crawl/crawldb $SEGMENT -filter -normalize

Create a link database:

    bin/nutch invertlinks crawl/linkdb -dir crawl/segments

### Send the Nutch crawled documents to Solr.

Important: The more number of times you repeat above crawling steps you will get better crawl depth! Indexing our crawl DB with Solr.

For Nutch 1.3 and lower.

    bin/nutch solrindex http://localhost:8080/solr/ crawl/crawldb crawl/linkdb crawl/segments/*

For Nutch 1.4 `-linkdb` needs to be specified.

    bin/nutch solrindex http://localhost:8080/solr/ crawl/crawldb -linkdb crawl/linkdb crawl/segments/*

### Search the crawled content in Solr

Now the indexed content is available through Solr. You can try to execute searches from the Solr admin UI from:

	http://localhost:8080/solr/admin

Or directly with url like:

	http://localhost:8080/solr/select/?q=usc&amp;version=2.2&amp;start=0&amp;rows=10&amp;indent=on&amp;wt=json

#### Search examplesbased on date ranges.

	timestamp:[* TO NOW]

	createdate:[1976-03-06T23:59:59.999Z TO *]

	createdate:[1995-12-31T23:59:59.999Z TO 2007-03-06T00:00:00Z]

	pubdate:[NOW-1YEAR/DAY TO NOW/DAY+1DAY]

	createdate:[1976-03-06T23:59:59.999Z TO 1976-03-06T23:59:59.999Z+1YEAR]

	createdate:[1976-03-06T23:59:59.999Z/YEAR TO 1976-03-06T23:59:59.999Z]

#### Delete contents based on search criteria.

Delete all documents that have the word—such as “foobar”—in the content field.

    curl http://localhost:8080/solr/update?commit=true -H "Content-Type: text/xml" --data-binary '<delete><query>content:foobar</query></delete>'

### Sundry Nutch related items.

Be sure to adjust your website’s the `robots.txt` file so the crawler can get better access and speed:

	User-agent: PrewornBot
	Crawl-delay: 1

#### Purge the Nutch and Solr data.

How to purge the Nutch crawl data:

	rm -rf /usr/share/apache-nutch-1.4/runtime/local/crawl/crawldb/current/*
	rm -rf /usr/share/apache-nutch-1.4/runtime/local/crawl/segments/*

Now delete the Solr index like this:

	curl http://localhost:8080/solr/update?commit=true -H "Content-Type: text/xml" --data-binary '<delete><query>*:*</query></delete>'

	Optimize the Solr index like this. If you check “Solr Statistics” and `maxDoc` is higher than `numDocs`, that means an optimization needs to take place.

	curl http://localhost:8080/solr/update?optimize=true

How to follow log files.

	sudo tail -f -n 200 /usr/share/apache-nutch-1.4/runtime/local/logs/hadoop.log
	sudo tail -f -n 200 /var/log/tomcat6/catalina.2015-10-14.log
	sudo tail -f -n 200 /var/log/tomcat6/catalina.out

***

<sup>*Java - Using a Web Crawler based on Solr and Nutch (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>
