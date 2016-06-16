## Nmap

By Jack Szwergold

Scan a range via a wildcard and returns hostname as well as IP:

	nmap -sP 192.168.1.*

Scan a range via a slash notation and returns hostname as well as IP:

	nmap -sP 192.168.1.1/24

Scan a range via a slash notation and checks if ports `80` and `8080` are open and returns hostname as well as IP:

	sudo nmap -v -O 192.168.1.1/24 -p80,8080

Scans all ports on `192.168.1.1` in verbose mode using the `-v` flag:

	nmap -v 192.168.1.1

Scans all ports on `192.168.1.1` in verbose mode using the `-v` flag and attempts to detect OS via the `-O` flag:

    sudo nmap -v -O 192.168.1.1

#### Port scanning a specific host.

A standard TCP port scan:

    sudo nmap -sS -Pn -p- -T4 -vv --reason -oN ~/nmap.TCP.results example.com

A standard UDP port scan:

    sudo nmap -sU -Pn -p- -T4 -vv --reason -oN ~/nmap.UDP.results example.com

A scan designed to attempt to discover versions of services on the server:

    sudo nmap -sV -Pn -p 22,80 -vv --reason -oN ~/nmap.SERVICE.results example.com

***

*Nmap (c) by Jack Szwergold; written on October 3, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
