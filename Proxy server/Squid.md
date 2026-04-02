 free and open source HTTP proxy that can be installed in a Linux server, squid can be very functional in the modern world, can be used as a distributed caching mechanism, a load balancer, or another component of a routing stack.
 squid by default listens on port 3128
### Install Squid
- Time directive:
	dead_peer_timeout 10 [seconds, minutes, hours]
- Size directive
	cache_mem 258 MB
- acl is only  indicating a range of ip addressees or a number of hosts
Commonly configured options include:
- `http_port`: Port to listen on for incoming proxy requests.
- `http_access` :Allow or deny access to certain HTTP requests

install Squid proxy by the following command:
	`sudo apt-get install squid`
check if the servers is installed
	`sudo systemctl status squid.service`
	
	Output
	● squid.service - Squid Web Proxy Server
     Loaded: loaded (/lib/systemd/system/squid.service; enabled; vendor preset>
     Active: active (running) since Fri 2023-08-18 11:00:36 CET; 23h ago
	`
By default Squid deny any clients to connect to it from outside of this server, in order to enable that, you need to make some changes to it's configuration file, open the config file by the following command:
	`sudo vim /etc/squid/squid.conf`
and search for the phrase `http_access deny all`
and you can see above this line that the `localhost` is allowed; and other connections are not.
	`http_access allow localhost`
so it's a good idea to keep the `deny all` rule at the bottom of this config block.
but you can change it to allow all:
	`http_access allow all`
or you can add a line above `http_access allow localhost` that includes your own IP address, like so:
`acl localnet src [YOUR_IP_ADDRESS]`

note that after any changes in the squid configuration you need to check and restart the service:
squid can parse and check its syntax with the following command: 
	`sudo squid -k parse`
and you can restart the service with the following command:
	`sudo systemctl restart squid.service`
#### test the proxy server connectivity
test if the proxy server is working with a simple curl request:
	`curl -x http://<squid-proxy-server-IP>:3128 -L http://google.com`

- The access control list (ACL) begins with an aclname and acltype followed by either:
	type-specific arguments or a quoted filename that they are read from..

	`acl aclname acltype argument...
	`acl aclname acltype "file"`... (the file should contain one item per line.)
you can check the available ACL types by the following link: [https://wiki.squid-cache.org/SquidFaq/SquidAcl]

Each ACL elements assigned a unique name. A named ACL element consists of a list of values, the multiple values uses OR logic.
You can put different values for the same ACL name on different lines.
You can't give the same name to two different type of ACL elements, it will generate syntax error

#### Access Lists
You can review all the access lists available by the following link [https://wiki.squid-cache.org/SquidFaq/SquidAcl#access-lists]
An access list consists of an allow or deny keyword , followed by list of ACL elements names.
An access list consists of one or more access list rules and they are checked in the order they are written.
if a rule has multiple ACL elements, it uses AND logic.which mean all the ACL elements of the rule must be a match in order for the rule to be a match.
	`http_access allow|deny acl_name1 acl_name2 ...`

- When building ACLs or configuration files for Squid, remember that the first match wins.
  therefore, start your ACLs with the most specific options in the beginning, and end your rules with an explicit rule that will match any transaction. Ex:
	  `http_access deny all`

### Configure Proxy Authentication
Along with access ACLs, you can add basic authentication to your proxy server for extra security.
step 1: install apache2-utils
	`sudo apt install apache2-utils`
step 2: add a user to the password file using htpasswd utility, this username and password will be used for all connections through this proxy.
	`sudo htpasswd -c /etc/squid/passwords ismail`
now you can see that the password and the username are stored in passwords file
	`sudo cat /etc/squid/passwords`
step 4: Add the following to the config file and save it.
	`aut_param basic program /usr/lib/squid3/basic_ncsa_auth /etc/squid/passwords
	`auth_param basic realm proxy
	`acl authenticated proxy_auth REQUIRED`
	`http_access allow authenticated`

step 5: restart the squid proxy server.
step 6: test the proxy connection using curl.
	`curl -x http://username:password@<squid-proxy-sever-IP:3128 http://www.google.com`
 