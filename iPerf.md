# iPerf

By Jack Szwergold, September 15, 2015

#### Basic iPerf usage.

iPerf is a network bandwidth testing tool that measures bandwidth between machines during a (default) 10 second test period. Results are reported in `Mbits/sec` but can be adjusted via the `-f` flag.

The first thing one needs to do to use iPerf is initiate an iPerf server on one machine using the `-s` flag like this:

	iperf -s

And on another client machine, initiate a client connection with the `-c` flag to the server setup in the first step; replace `[ip or hostname of server]` with the actual IP address or hostname of the server being connected to:

	iperf -c [ip or hostname of server]

This is a one way connection test between client and server so if there is a need to test speed in the other direction, the steps need to be swapped between machines; client becomes server and server becomes client.

#### Bandwidth output options.

Bandwidth output options via the `-f` flag; default is `Mbits/sec`:

	'b' = bits/sec
	'k' = Kbits/sec
	'm' = Mbits/sec
	'B' = Bytes/sec
	'K' = KBytes/sec
	'M' = MBytes/sec

#### Some advanced iPerf examples.

Start the server with port `65000`:

    iperf -s -p 65000

Client machine connects to the server (above) with port `65000`:

    iperf -c [ip or hostname of server] -p 65000

Set the interval to be 2 seconds between tests:

    iperf -c [ip or hostname of server] -i 2

Set the interval to be 2 seconds between tests and set the time in seconds to transmit 60 seconds (instead of the default 10 seconds)

    iperf -c [ip or hostname of server] -i 2 -t 60

Set the interval to be 2 seconds between tests and run it as a dual test to/from server:

    iperf -c [ip or hostname of server] -i 2 -d

Set the interval to be 2 seconds between tests, run it as a dual test to/from server and set the time in seconds to transmit 60 seconds (instead of the default 10 seconds):

    iperf -c [ip or hostname of server] -i 2 -d -t 60

#### The man page for iPerf.

	IPERF(1)                         User Manuals                         IPERF(1)
	
	NAME
	       iperf - perform network throughput tests
	
	SYNOPSIS
	       iperf -s [ options ]
	
	       iperf -c server [ options ]
	
	       iperf -u -s [ options ]
	
	       iperf -u -c server [ options ]
	
	DESCRIPTION
	       iperf is a tool for performing network throughput measurements.  It can
	       test either TCP or UDP throughput.  To perform an iperf test  the  user
	       must establish both a server (to discard traffic) and a client (to gen-
	       erate traffic).
	
	GENERAL OPTIONS
	       -f, --format
	              [kmKM]   format to report: Kbits, Mbits, KBytes, MBytes
	
	       -h, --help
	              print a help synopsis
	
	       -i, --interval n
	              pause n seconds between periodic bandwidth reports
	
	       -l, --len n[KM]
	              set length read/write buffer to n (default 8 KB)
	
	       -m, --print_mss
	              print TCP maximum segment size (MTU - TCP/IP header)
	
	       -o, --output <filename>
	              output the report or error message to this specified file
	
	       -p, --port n
	              set server port to listen on/connect to to n (default 5001)
	
	       -u, --udp
	              use UDP rather than TCP
	
	       -w, --window n[KM]
	              TCP window size (socket buffer size)
	
	       -B, --bind <host>
	              bind to <host>, an interface or multicast address
	
	       -C, --compatibility
	              for use with older versions does not sent extra msgs
	
	       -M, --mss n
	              set TCP maximum segment size (MTU - 40 bytes)
	
	       -N, --nodelay
	              set TCP no delay, disabling Nagle's Algorithm
	
	       -v, --version
	              print version information and quit
	
	       -V, --IPv6Version
	              Set the domain to IPv6
	
	       -x, --reportexclude
	              [CDMSV]   exclude C(connection) D(data) M(multicast) S(settings)
	              V(server) reports
	
	       -y, --reportstyle C|c
	              if set to C or c report results as CSV (comma separated values)
	
	SERVER SPECIFIC OPTIONS
	       -s, --server
	              run in server mode
	
	       -U, --single_udp
	              run in single threaded UDP mode
	
	       -D, --daemon
	              run the server as a daemon
	
	CLIENT SPECIFIC OPTIONS
	       -b, --bandwidth n[KM]
	              set  target  bandwidth to n bits/sec (default 1 Mbit/sec).  This
	              setting requires UDP (-u).
	
	       -c, --client <host>
	              run in client mode, connecting to <host>
	
	       -d, --dualtest
	              Do a bidirectional test simultaneously
	
	       -n, --num n[KM]
	              number of bytes to transmit (instead of -t)
	
	       -r, --tradeoff
	              Do a bidirectional test individually
	
	       -t, --time n
	              time in seconds to transmit for (default 10 secs)
	
	       -F, --fileinput <name>
	              input the data to be transmitted from a file
	
	       -I, --stdin
	              input the data to be transmitted from stdin
	
	       -L, --listenport n
	              port to recieve bidirectional tests back on
	
	       -P, --parallel n
	              number of parallel client threads to run
	
	       -T, --ttl n
	              time-to-live, for multicast (default 1)
	
	       -Z, --linux-congestion <algo>
	              set TCP congestion control algorithm (Linux only)
	
	ENVIRONMENT
	       TCP_WINDOW_SIZE
	              Controls the size of TCP buffers.
	
	DIAGNOSTICS
       This section needs to be filled in.

	BUGS
	       Exit statuses are inconsistent.  The threading implementation is rather
	       heinous.
	
	AUTHORS
	       Iperf  was  originally  written by Mark Gates and Alex Warshavsky.  Man
	       page and maintence by Jon Dugan <jdugan at x1024 dot net>.  Other  con-
	       tributions  from  Ajay  Tirumala,  Jim Ferguson, Feng Qin, Kevin Gibbs,
	       John Estabrook <jestabro at ncsa.uiuc.edu>, Andrew  Gallatin  <gallatin
	       at gmail.com>, Stephen Hemminger <shemminger at linux-foundation.org>
	
	SEE ALSO
	       http://iperf.sourceforge.net/
	
	NLANR/DAST                        APRIL 2008                          IPERF(1)

***

*iPerf (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*