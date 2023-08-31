
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



---

# Tags

#machine #squidproxies 