Dns also can translate IP's into names
this example of a PTR record for an IPv4 address shows the nibble format of the reverse of Google's IPv4 DNS server:

	46.204.250.142.in-addr.arpa. 7191 IN	PTR	hkg07s38-in-f14.1e100.net.

the command line tool dig with the -x flag can be used to look up the reverse DNS name of an IP address 

		dig -x ip_address