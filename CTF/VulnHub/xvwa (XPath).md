---
tags:
  - machine
  - vulnhub
  - xpath
  - arp-scan
  - nmap
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

The first is discover how many primary tags are in the xml file, in this case only one **Coffees** 

With this query you are counting the primary tags that have the document

````bash
search=1' and count(/*)='1&submit=
````

The second quote is for closing the quote that we don't see.

If you put 2, you don't see the product, this mean that **only exists one** primary tag (remember that primary tag in this case is **Coffees**)

