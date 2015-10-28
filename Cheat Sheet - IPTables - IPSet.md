# Cheat Sheet - IPSet

By Jack Szwergold, October 27, 2015

#### Trying new ideas. This isn’t that great due to memory usage.

	sudo aptitude install ipset
	
	sudo ipset -N china hash:net
	
	sudo rm -f cn.zone
	
	curl -O -L http://www.ipdeny.com/ipblocks/data/countries/cn.zone
	
	for ip_range in $(cat cn.zone); do sudo ipset -A china $ip_range; done
	
	sudo iptables -A INPUT -p tcp -m set --match-set china src -j DROP

***

*Cheat Sheet - IPSet (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
