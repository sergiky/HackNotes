
Welcome to everyone, today we are going exploiting [SickOS 1.1](https://www.vulnhub.com/entry/sickos-11,132/) machine in [VulnHub](https://www.vulnhub.com/) platform

We have to download the ZIP and export this in our app ([Oracle Vritual Box](https://www.virtualbox.org/) or [VMWare](https://www.vmware.com/es/products/workstation-player/workstation-player-evaluation.html))

---

# Recon


The first of all we are going to do a local scan to identify the host in our network:

We are going to use [arp-scan](../../Tools/Enumeration/arp-scan/arp-scan.md)

````bash
sudo arp-scan -l --ignoredups
````

`-l` --> Do the arp scan in our **local network**

This use the local adapter, in my case is eth0, if you want to indicate other that are not by default, you can use `-I interfacename`

`--ignoredups` --> This switch is used to avoid duplicated IP 

![](../../Images/Pasted%20image%2020230831173638.png)

Now we are going to create our works directory

> mkdir {nmap.data,exploit}

Now we are going to try if the machine is available

> ping -c 1 192.168.1.38


![](../../Images/Pasted%20image%2020230831173723.png)

Don't worry if the ping don't work, if you realize an port scan with [Nmap](../../Tools/Enumeration/Nmap/Nmap.md), you can see that return some open ports

````bash
sudo nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 192.168.1.38 -oG allPorts
````

![](../../Images/Pasted%20image%2020230831173703.png)

Now with [ExtractPorts](../../Utils/S4vitaar/ExtractPorts/ExtractPorts.md) (util from [S4vitaar](https://www.youtube.com/s4vitar)) we can copy the ports to the clipboard and the information looks better.

![](../../Images/Pasted%20image%2020230831174725.png)

Now we can use an advance scan to discover the service and version that are running for this ports.

````bash
sudo nmap -sCV -p22,3128 192.168.1.38 -oN targeted
````

`-sCV` --> C launch some scripts to enum the services, and V for know the version of the services, these are two separated switches but can be used together

`-oN` --> Export the information in a file with **nmap format** (how you see in the output)

![](../../Images/Pasted%20image%2020230831175056.png)

We do a curl, but we don't get anything

````bash
curl http://website
````

In port scanning you don't have to see the 80 port open, if you realize an scan of the port 80 with nmap, they say that is **filtered**

````bash
sudo nmap -p80 192.168.1.38 -T5 -v -n
````

![](../../Images/Pasted%20image%2020230831175504.png)


You can try to use a proxy with curl command:
````bash
curl http://192.168.1.38 --proxy http://192.168.1.38:3128
````

Remember that the port 3128 was reported by nmap when scanning ports.

You can add an entry in [FoxyProxy](https://addons.mozilla.org/es/firefox/addon/foxyproxy-standard/): 

![](../../Images/Pasted%20image%2020230831175738.png)

If you enable it and put the ip you can see the website

Now you can use [Gobuster](../../Tools/Enumeration/Gobuster.md) to enum directories with the switch **--proxy **

````bash
gobuster dir -u http://192.168.1.38 --proxy http://192.168.1.38:3128 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
````

![](../../Images/Pasted%20image%2020230831180156.png)

You can enumerate the internal port with an script in Python, this is in [Enumeration and exploiting SQUID Proxies](../../OWASP%20TOP%2010%20and%20web%20vulnerabilities/Enumeration%20and%20exploiting%20SQUID%20Proxies/Enumeration%20and%20exploiting%20SQUID%20Proxies.md)



![](../../Images/Pasted%20image%2020230831183405.png)

We can see in the internal ports that the service 80 and 3306 (MySQL) are exposed

We can do a curl to the port 3306

> curl http://127.0.0.1:3306 --proxy http://192.168.1.38:3128

![](../../Images/Pasted%20image%2020230831183554.png)

They say that we have to use the switch **--output** because is a binary file

> curl http://127.0.0.1:3306 --proxy http://192.168.1.38:3128 --output output

![](../../Images/Pasted%20image%2020230831183740.png)

Sometimes you need an **authentication**, you can this in the url:

> curl http://website --proxy http://admin:password@192.168.1.12:3128

---

# Tags

#machine #squidproxies #shellshock 