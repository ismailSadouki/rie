DNS or Domain name system, is the backbone of the internet and is the service that runs in all our computers but obviously, it's run mostly over the internet public service.
the idea is that computers need IP addresses to communicate with each other, But the problem with that is that we as humans can't remember all the IP addresses of all the services, also IP addresses change over time, so with help of DNS it give us a human-readable name that we can remember easily, then DNS takes that name and turn it to an ip and then you can actually communicate with that server

in easy words, if DNS isn't working then you will be unable to connect to other computers on the internet using their name, but you can still connect using their IP address if you know it.

#### Why Dns Matters?
as humans we remember names, DNS names what everyone remembers,for example let's assume that we have this list: 
		 example.com => 45.51.33.23
		 example2.com => 45.87.34.22
		 example3.com => 45.89.23.12
		 example4.com => 45.16.84.23
which is easier to remember, the IP addresses or the domain names?

### How Dns Works
when you type a URL into the browser it makes a DNS query to figure out which unique ip address is associated with that hostname, first it will look into the local browser or OS cache but if the cache is empty then it will look into a server known as the dns recursive resolver.

### Dns structure
The domain name is made of multiple parts, called labels. The domain hierarchy is read from right to left.
		www.google.com. -> FQDN (Fully qualified domain name) 
		. => root (the dot in the end of the FQDN)
		.com => (TLD) top level domain name
		.google => second level domain name
		WWW => sub-domain (hostname)




