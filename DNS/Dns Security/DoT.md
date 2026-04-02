first check the following article: https://www.cloudflare.com/learning/dns/dns-over-tls/
DNS over TLS is very easy to detect because it uses the port 853, you can use it in Linux by enabling `DNSOverTLS` in the file `/etc/systemd/resolved.conf `, and change your DNS recursive resolver into a server that support DoT like cloudflare `1.1.1.1` or quad9 `9.9.9.9` , then restart the resolved service `sudo systemctl restart systemd-resolved.service` and the NetworkManeger when you change your DNS recursive resolver `sudo systemctl restart NetworkManager.service`  

	`
	step 1:
	sudo vim /etc/systemd/resolved.conf
	setp 2:
	DNSOverTLS=yes
	step 3:
	// make sure your using a DNS server that support DoT
	step 4:
	sudo systemctl restart systemd-resolver.service
	step 5:
	sudo systemct restart NetworkManager.service
	`
to check if you are using DoT use the following command:
	` step 6:`
	 `sudo tcpdump -i any 'port 853' ` 
or you can  test your dns resolvers by visiting the following websites: 
	`https://one.one.one.one/help/`
	`https://dnscheck.tools/#results`
	
