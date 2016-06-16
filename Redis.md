## Redis

By Jack Szwergold

### Sundry Redis items.

Redis should now be installed and you can check the version by running:

    redis-server --version

The version number should read like this:

	Redis server v=3.0.4 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 build=e10d4bb04434c274

You can check if it’s running by doing an `nmap` scan on port `6379` like this:

    nmap localhost -p6379

And the port should show as open like this:

	Starting Nmap 5.21 ( http://nmap.org ) at 2015-09-22 20:48 EDT
	Nmap scan report for localhost (127.0.0.1)
	Host is up (0.00011s latency).
	PORT     STATE SERVICE
	6379/tcp open  unknown
	
	Nmap done: 1 IP address (1 host up) scanned in 0.06 seconds

You can “ping” Redis like this:

    redis-cli ping

The response should be:

    PONG

### Sundry Redis items on an Ubuntu/Debian system.

Start, stop and control Apache on an Ubuntu/Debian system:

    sudo service redis-server start
    sudo service redis-server stop
    sudo service redis-server restart
    sudo service redis-server force-reload
    sudo service redis-server status

### Benchmark Redis performance.

Run this command to benchmark Redis:

    redis-benchmark

The output should be something like this:

	====== PING_INLINE ======
	  100000 requests completed in 1.56 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	98.10% <= 1 milliseconds
	99.97% <= 2 milliseconds
	100.00% <= 2 milliseconds
	64267.35 requests per second
	
	====== PING_BULK ======
	  100000 requests completed in 1.67 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	96.61% <= 1 milliseconds
	99.91% <= 2 milliseconds
	99.95% <= 3 milliseconds
	99.96% <= 4 milliseconds
	100.00% <= 4 milliseconds
	59701.50 requests per second
	
	====== SET ======
	  100000 requests completed in 1.50 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	97.84% <= 1 milliseconds
	99.90% <= 2 milliseconds
	100.00% <= 2 milliseconds
	66755.67 requests per second
	
	====== GET ======
	  100000 requests completed in 1.55 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	97.65% <= 1 milliseconds
	99.99% <= 2 milliseconds
	100.00% <= 2 milliseconds
	64474.53 requests per second
	
	====== INCR ======
	  100000 requests completed in 1.44 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	98.38% <= 1 milliseconds
	100.00% <= 2 milliseconds
	100.00% <= 2 milliseconds
	69300.07 requests per second
	
	====== LPUSH ======
	  100000 requests completed in 1.52 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	96.64% <= 1 milliseconds
	99.83% <= 2 milliseconds
	100.00% <= 2 milliseconds
	65573.77 requests per second
	
	====== LPOP ======
	  100000 requests completed in 1.50 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	95.94% <= 1 milliseconds
	99.93% <= 2 milliseconds
	100.00% <= 2 milliseconds
	66622.25 requests per second
	
	====== SADD ======
	  100000 requests completed in 1.86 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	87.32% <= 1 milliseconds
	99.58% <= 2 milliseconds
	100.00% <= 2 milliseconds
	53850.30 requests per second
	
	====== SPOP ======
	  100000 requests completed in 1.66 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	94.01% <= 1 milliseconds
	99.88% <= 2 milliseconds
	100.00% <= 3 milliseconds
	60096.15 requests per second
	
	====== LPUSH (needed to benchmark LRANGE) ======
	  100000 requests completed in 1.59 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	95.58% <= 1 milliseconds
	99.93% <= 2 milliseconds
	100.00% <= 2 milliseconds
	62853.55 requests per second
	
	====== LRANGE_100 (first 100 elements) ======
	  100000 requests completed in 3.82 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	31.02% <= 1 milliseconds
	93.52% <= 2 milliseconds
	99.28% <= 3 milliseconds
	99.83% <= 4 milliseconds
	99.92% <= 5 milliseconds
	99.95% <= 6 milliseconds
	99.99% <= 7 milliseconds
	100.00% <= 7 milliseconds
	26164.31 requests per second
	
	====== LRANGE_300 (first 300 elements) ======
	  100000 requests completed in 8.72 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	0.00% <= 1 milliseconds
	30.72% <= 2 milliseconds
	96.48% <= 3 milliseconds
	99.70% <= 4 milliseconds
	99.97% <= 5 milliseconds
	100.00% <= 5 milliseconds
	11471.84 requests per second
	
	====== LRANGE_500 (first 450 elements) ======
	  100000 requests completed in 12.14 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	0.00% <= 1 milliseconds
	0.00% <= 2 milliseconds
	67.43% <= 3 milliseconds
	94.90% <= 4 milliseconds
	98.73% <= 5 milliseconds
	99.79% <= 6 milliseconds
	99.95% <= 7 milliseconds
	99.99% <= 8 milliseconds
	100.00% <= 8 milliseconds
	8235.20 requests per second
	
	====== LRANGE_600 (first 600 elements) ======
	  100000 requests completed in 15.23 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	0.00% <= 2 milliseconds
	0.15% <= 3 milliseconds
	79.23% <= 4 milliseconds
	96.93% <= 5 milliseconds
	99.16% <= 6 milliseconds
	99.87% <= 7 milliseconds
	99.98% <= 9 milliseconds
	100.00% <= 9 milliseconds
	6566.42 requests per second
	
	====== MSET (10 keys) ======
	  100000 requests completed in 2.22 seconds
	  50 parallel clients
	  3 bytes payload
	  keep alive: 1
	
	58.76% <= 1 milliseconds
	97.09% <= 2 milliseconds
	99.65% <= 3 milliseconds
	99.98% <= 4 milliseconds
	100.00% <= 4 milliseconds
	45065.34 requests per second

### Practical Redis data usage examples.

Now you can do simple tests via the Redis CLI like this:

    redis-cli

Set a value to a key like this; the response should be “OK”:

	redis 127.0.0.1:6379> set mykey somevalue
	OK

Get a value connected to a key like this; the response should be “somevalue”:

	redis 127.0.0.1:6379> get mykey
	"somevalue"

***

*Redis (c) by Jack Szwergold; written on September 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*