
![](../../../Images/bae443f73a706fc8eebc6fb740128295.png)

- SMB Enumeration


---

# Walkthrough

## Recon

### Check the connectivity with the machine

We try the connectivity with the machine, for this we send a trace ICMP

> ping -c 1 10.10.11.151

![](../../../Images/Pasted%20image%2020230815181532.png)

The machine is on and prepared to be hacked,** by default the TTL of Windows machine are 128 and in Linux machine are 64**, more information [here](https://subinsb.com/default-device-ttl-values/), in this case is closer to 128.

---

### Search open ports

One time we checked that the machine is available we can do a port scanning with [[Rustscan]] or [[Nmap]]  

> rustscan --scan-order random -a 10.10.11.152 -- oG allPorts -sCV targeted

`--scan-order random` --> search ports random, to avoid firewall (in this case you can ommit this switch)

`--` --> When you indicate the two hyphens means that you are going to indicate nmap switches


> sudo nmap -p- --open -sS --min-rate 4500 -vvv -n -Pn 10.10.11.152 -oG allPorts

`-p-` --> Scan all the ports available (65535)
`--open` --> Only report the open ports
`-sS` --> Use [tcp Syn Port Scan](https://nmap.org/book/synscan.html)
`--min-rate 4500` --> Don't take package that are more slowly than 4500 packages by seconds
`-vvv` --> To report the ports when they are discovery
`-n` --> Avoid DNS resolution that extend a little the scan innecseary
`-Pn` --> skip host Discovery (check if the host is active)
`-oN` --> Exports the result in grepeable format


![](../../../Images/Pasted%20image%2020230815183437.png)

### See service and version that are running in the ports

When the scan finish we have an an util named **extractPorts** created by [S4vitar](https://www.youtube.com/c/s4vitar) that collect the ports and copy them in the clipboard, you can put this code in your bashrc or zshrc config file

````bash
# extractPorts
extractPorts () {
	ports="$(cat $1 | grep -oP '\d{1,5}/open' | awk '{print $1}' FS='/' | xargs | tr ' ' ',')" 
	ip_address="$(cat $1 | grep -oP '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}' | sort -u | head -n 1)" 
	echo -e "\n[*] Extracting information...\n" > extractPorts.tmp
	echo -e "\t[*] IP Address: $ip_address" >> extractPorts.tmp
	echo -e "\t[*] Open ports: $ports\n" >> extractPorts.tmp
	echo $ports | tr -d '\n' | clip.exe
	echo -e "[*] Ports copied to clipboard\n" >> extractPorts.tmp
	/bin/batcat extractPorts.tmp
	rm extractPorts.tmp
}
````

Now you can inspect the service and version that are running in this port:

````bash
sudo nmap -sCV -p53,88,135,139,389,445,464,593,636,3268,3269,5986,9389,49667,49673,49674,49696,62388 10.10.11.152 -oN targeted
````

`-sCV` --> This are two switches in one (-sC & -sV) they send some scripts to recon service and version
`-p` --> Inidicate the port that you want to scan
`-oN` --> Report the information in format nmap and in a file named targeted

![](../../../Images/Pasted%20image%2020230815184310.png)

### Scanning smb service

While this scan is being carried out, we see that the port 445 is open ([smb](https://es.wikipedia.org/wiki/Anexo:Puertos_de_red)) more information about services in ports in [Wikipedia web page](https://es.wikipedia.org/wiki/Anexo:Puertos_de_red).

We can use [[Crackmapexec]] for recon the smb of the victim machine

> crackmapexec smb 10.10.11.152

![](../../../Images/Pasted%20image%2020230815185202.png)

We can see the name of the machine, that is DC01 ([Domain Controller](https://www.techtarget.com/searchwindowsserver/definition/domain-controller)) this is a failed because the objective of an attacker is hack the machine with more privilege (in this case domain controller), in case that the name don't indicate if it is a domain controller or not, when you are inside the machine you can see which is the gateway (the gateway in [Active directory](https://en.wikipedia.org/wiki/Active_Directory) normally is the domain controller)

And they report the version of Windows, name of the domain and a little more information.

### Virtual hosting

I recommend to put the name in /etc/hosts because in some attacks we need to put the name of the domain

> sudo nvim /etc/hosts

![](../../../Images/Pasted%20image%2020230815190000.png)

And now you can try to do a ping to the domain:

> ping -c 1 timelapse.htb

### List shared resources with a null session

We can use [[smbclient]]

> smbclient -L 10.10.11.152 -N 

`-L` --> To list the resource 
`-N` --> Indicate that we are going to connect using null session (without password)

![](../../../Images/Pasted%20image%2020230815191441.png)

Sometimes we can use [[Crackmapexec]] with the switch **--shares**

> crackmapexec 10.10.11.152 --shares

But not always work, sometimes this breaks and don't report nothing

![](../../../Images/Pasted%20image%2020230815191506.png)

Now we are going to use [[smbmap]], the good part of this tool is that report the privilege if you can write, read...

> smbmap -H 10.10.11.152

![](../../../Images/Pasted%20image%2020230815191532.png)

If you see that this don't work you can use the switch ``-u`` (user)

> smbmap -H 10.10.11.152 -u "null"

![](../../../Images/Pasted%20image%2020230815191636.png)

We can see the resources of **IPC$** and **Shares** folders

To connect to the server we can use this command:

> smbclient //10.10.11.152/Shares -N

![](../../../Images/Pasted%20image%2020230815191954.png)

You can use dir to list the resources in the folder, we can move to the directory Dev

> cd Dev

![](../../../Images/Pasted%20image%2020230815192100.png)

we can go back with cd .. and see the content of the other folder

![](../../../Images/Pasted%20image%2020230815192328.png)

We can see some documents that begin with LAPS, what is [LAPS](https://learn.microsoft.com/en-us/windows-server/identity/laps/laps-overview)?

Windows Local Administrator Password Solution.
This tool provide a password to administrator users

### Download winrm_backup.zip file

But we are going to download the winrm_backup.zip

> get winrm_backup.zip

![](../../../Images/Pasted%20image%2020230815193414.png)

We can exit of the session

> exit

And we have the zip file.

We can list the resources of the zip with the command

> 7z l winrm_backup.zip

![](../../../Images/Pasted%20image%2020230815193650.png)

What is this file?
If we search what type of files have extensions **.pfx** we find that are a backup of private certificate used to authenticate a person or a device, such as a computer or a web server


---

# Resources

- https://infosecmachines.io/
- Based in [s4vitaar video](https://www.youtube.com/watch?v=NnlYSY83EsA)

---
# Tags

#machine #windows #activedirectory #crackmapexec #smbclient