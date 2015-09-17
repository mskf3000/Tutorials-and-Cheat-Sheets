# Cheat Sheet - DIG

By Jack Szwergold, September 15, 2015

#### Basic `dig` commands.

Detailed DNS info for a hostname:

    dig +nocmd [hostname] any +multiline +noall +answer

Look up the TTL for a hostname:

    dig +nocmd [hostname] +noall +answer

Look up the authoritative `NS` (namservers) for a hostname:

    dig NS [hostname]

DNS lookup of a hostname on a specific nameserver:

    dig @[dns server address] NS [hostname]

DNS lookup of a hostname on a specific nameserver using OpenDNS’s DNS servers:

	dig @208.67.222.222 NS [hostname]
	dig @208.67.222.220 NS [hostname]

DNS lookup of a hostname on a specific nameserver using Google’s DNS servers:

	dig @8.8.8.8 NS [hostname]
	dig @8.8.4.4 NS [hostname]

***

*Cheat Sheet - DIG (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*