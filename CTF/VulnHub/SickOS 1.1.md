
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

![](../../Images/Pasted%20image%2020230830185735.png)

Now we are going to create our works directory

> mkdir {nmap.data,exploit}

Now we are going to try if the machine is avaialable

> ping -c 1 192.168.1.34


![](../../Images/Pasted%20image%2020230830190148.png)

Now we are going to scan ports with [Nmap](../../Tools/Enumeration/Nmap/Nmap.md)

````bash
sudo nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 192.168.1.34 -oG allPorts
````



---

# Tags

#machine #squidproxies 