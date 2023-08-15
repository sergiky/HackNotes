View information about certification of SSL/TLS (in HTTPS websites)
To see the certificates you can see them in explorer or in terminal with:
````bash
openssl s_client -connect tinder.com:443
````


Inspect the certificate in search of vulnerabilities
````bash
sslscan tinder.com
````

nmap have a script 
````bash
nmap --script ssl-heartbleed -p8443 127.0.0.1
````