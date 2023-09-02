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