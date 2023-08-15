# Theory
Exists 65536 port but the 0 is reserved and only can use 65535

The well-know port are 0-1024, but it's the same the port 0 is reserved and can't use
## Types of scan
- `TCP Connect Scans (-sT)` --> Send TCP packets, if the target system respond with a SYN-ACK packet, **the port is open**.
- `SYN "HALF-open" Scans (-sS)` --> nmap don't respond with the final ACK packet and don't establish the full connection. It's **more difficult to detect** because the connection is not full establish
- `UDP Scans (-sU) ` --> Send UDP packet. If the target response is a ICMP packet is close, but it doesn't respond the **port is open**
- `TCP Null Scan (-sN)` --> The flag header is 0. If the target send RST packet that say that the port is close, but if we didn't receive them that means that is open. This type of scan can by useful for bypassing some types of firewall protections that block other type of scan.
- `TCP FIN Scan (-sF)` --> Send TCP packet with FIN (finish) flag, if the target don't respond with a RST packet that means that is open. It's useful to bypass some firewall.
- `TCP Xmas Scan (-sX)` --> send TCP package with flags FIN, PSH (push) and URG (urgent)

### TCP Scan
This work by three-way handshake (three stages):
1. Our machine send a TCP request to the target with the **SYN flag**, their intention is to establish a connection with the target.
2. The target responds with a **SYN/ACK** flag that is posible to establish a connection.
3. Our machine responds with a **ACK** flag indicating that it has received the packet and is ready to communicate (**port open**)
![](../../../Images/Pasted%20image%2020230512000223.png)

But if the connection it isn't allowed the server send tcp packet with a **RST** (reset) flag (**port closed**).

![](../../../Images/Pasted%20image%2020230512001243.png)

Sometimes when a port is open but is hidden behind a firewall the nmap say *filtered*, this means that the firewall is drop the tcp packet and the server **don't send any packet**. 

Some firewalls are configure to respond with a RST TCP packet. This complicates the detection a lot.

### SYN Scans / stealth scans / half-open
The difference between TCP Scan is that SYN scans send a **RST** TCP packet after receiving SYN/ACK packet. The rules to identify open and close ports are the same that TCP scan, if a port is closed the server respond with a **RST TCP packet**, if the port is filtered by a firewall the SYN packet is dropped.

![](../../../Images/Pasted%20image%2020230514233359.png)

#### ¿Which are the advantages?
- It can be used to bypass **older** Intrusion Detection systems that are looking for full three way handshake
- Normaly the SYN scansnot logged by applications because they save the log when the connection is fully established, but this is not the case with this type of scan
- This types of scan are more faster because they don't finish the full three way handshake

**Syn scans are used for default when you run nmap with sudo permission**, in case that you execute the program without this permission, the scan by default is TCP.

#### Disadvantages
- They require sudo permission. This is because they require the ability to create raw packets (allow to have a control of the content of the packet)
- Unastable services sometimes can get down

# Parameters with examples

### How enum port and ranges

A single port:
````bash
nmap -p22 IP
````
Range of port:
````bash
nmap -p1-1024 IP
````

to enum all ports we can use -p-:
````bash
nmap -p- IP
````

If you want to scan the 500 ports most used, you can put 1000, 1500, 100
````bash
nmap --top-ports 500 IP
````

If you put this, you only  enumerate the most used port**
````bash
nmap IP
````

### Open | filetered | closed

If you only want that the report only contain open ports you can use **--open**
````bash
nmap -p- --open IP
````
When you can't see any port open you can search filtered ports

#### -v | -vvv
if you want to see the progress you can use the parameter -v
````bash
nmap -p- --open -v IP
````

## TCP | UDP ...

### TCP Connect Scan ( -sT )
The default scan operate with this type of scan, is not necessary to put the parameter
````bash
nmap -p22 -sT --open -v -n IP
````


### DNS resolution
Translate the name to IP, this slows down our scan, for submit that we can use the param **-n**
````bash
nmap -p- --open -v -n IP
````

### -T
We can adjust the velocity of the scan

This parameter have different modes:
0 --> Mode Paranoid
`1` --> Mode Stealthy
`2` --> Mode kind
`3` -->Mode normal
`4` --> Mode Agressive
``5`` --> Mode crazy 

Example:
````bash
nmap -p- -T5 --open -n -v IP
````


### -Pn
By default nmap check if the hosts are active (**host discovery**) if you know that the host are active, you can use -Pn to omit this
````bash
nmap
````

### ARP Scan with nmap ( -sn )
They do ping to all the network
````bash
nmap -sn 192.168.1.0/24
````

### -O
To detect the operating system, this is very noisy it isn't recomendable
````bash
nmap -O IP
````

### -sV
To detect the service and version that are using the inidicated ports, this is called fingerprinting
````bash
nmap -p22,80 -sV IP
````

### UDP Scans
The packet that send to the target can arrive or not. This type of packet are very faster becuase they don't have response ( for nmap the port that don't have response is open|filtered ), but if the port have a UDP response (is very unusual) the port is open.
The port is close when receive a ICMP packet (ping) saying that is unreachable

This is very **slow** to realice, for this is used with ``--top-ports <number>``, `--top-ports 20` the 20 ports more used in UDP

#### nmap switch
- **-sU**

#### Examples UDP Scan

UDP scan are most slowly than TCP
````bash
nmap -p- --open -v -n -Pn -sU IP
````

We can use other param to speed up
````bash
nmap --top-ports 500 -v -n -Pn -sU IP
````


### NULL, FIN and Xmas
More information in tryhackme furthernmap
They generally used for firewall evasion

## ICMP Scanning
In the first connection, we need to do a map of the network structure. We can see what host are active or not.

With nmap we can do **ping sweep**.
We can send a ping to all host of a network range.

To do that we use **-sn** that tells to nmap that don't scan any port (except 80 and 443) and send ICMP packets

### Example
````bash
nmap -sn 192.168.0.1-254

nmap -sn 192.168.0.0/24
````

## NSE scripts
**N**map **S**cripting **E**ngine ( NSE ), they are written in Lua.

### Categories
- ``safe``: Don't affect the target
- `intrusive`: This is not safe, they affect target
- `vuln`: Scan for vulnerabilities. Take care with this script, we can loose the control
- ``exploit``: Attempt to exploit vulnerabilities
- `auth`: Attempt to bypass authentication for running services like ftp server anonymously
- `brute`: Attempt to brute force credentials in running services
- `discovery`: Attempt to query services for information about network.
- `dos`: To test if is vulnerable to a Ddos
...
### How to work with them

To run any script of a category we can use
````bash
--script=safe
````

Launch script that belong to two categories
````bash
--script="vuln and safe"
````

Or scripts that have one category or other
````bash
--script="vuln or safe"
````

To run a specific script we use:
````bash
--script=http-fileupload-exploiter
````

And multiple script:
````bash
--script=smb-enum-users,smb-enum-shares
````

Some script require some arguments
This example take two arguments, the location of the file and
````bash
nmap -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'
````

The arguments are **url** (http-put.url) and **file** (http-put.file) 


remember that all scripts have help menus, you can use:
````bash
nmap --script-help script-name
````

### How to search NSE scripts
We have two option for this, search in the [nmap website](https://nmap.org/nsedoc/) or we can search in our local storage, all the scripts are at /usr/share/nmap/scripts.

We have two option to see the installed script
One is using the /usr/share/nmap/scripts/scripts.db, we can **grep** the key words to find scripts
````bash
grep "ftp" /usr/share/nmap/script.db
````

The second form is using the ls command:
````bash
ls -l /usr/share/nmap/scripts/*ftp*
````

Remember that the asterisks  complete the rest of the characters

### How to intall new script
If we lose some scripts we can update and reinstall nmap
````bash
sudo apt update && sudo apt install nmap
````

Or we can install scripts manually one by one
````bash
sudo wget -O /usr/share/nmap/scripts/<script-name>.nse https://svn.nmap.org/nmap/scripts/<script-name>.nse
````

You can do your **own script** in LUA for nmap

Don't forget update the script.db, because nmap use this for internal engineer.
````bash
nmap --script-update-db
````

### Create scripts

````lua
-- HEAD --

description = [[
Script de ejemplo que enumera y reporta puertos abiertos por TCP
]]

-- RULE

portrule = function(host,port)
        return port.protocol == "tcp" -- Comprobar si el protocolo es tcp
                and port.state == "open" -- Comprobar que el puerto está abierto
end
-- ACTION

action = function(host, port)
        return "This port is open!"
end
````

`head` --> In this zone you put the description, dependencies, requirements...
`rule` -->In this section you define the necessary condition to choose the correct ports
``action`` --> What action do when detect a open port. You can do a lot of things, like logging to the service in that port or return a string with information
````lua
print("Open port found:", port.number)
````

When we execute then with:
````bash
nmap --script /home/sergio/Desktop/Academia/example.nse -p22 192.168.1.1
````

You obtain that:
````java
Nmap scan report for 192.168.1.1
Host is up (0.0047s latency).

PORT   STATE SERVICE
22/tcp open  ssh
|_example: This port is open!

Nmap done: 1 IP address (1 host up) scanned in 1.23 seconds
````

# Firewall evasion or IDS ( Intrusion detection system)
Nmap have a param **-Pn** that tells to nmap that don't do ping to the host before scan it. This means that nmap** always treat the target host(s) as begin alive** and if the target host have a ICMP block they don't detected. The wrong part of this is that the scan **take a long time**. in the case that the host was dead the nmap was checking port and double checking the port.

==Important!==
Don't do that were you are in the local network, you can use the **ARP requests** to determiny the host activity

Some switches that are used for evation firwall:

### -f
Fragment the packet,  when the packets are smaller it will be most dificult to be detected.
The firewalls wait a specify packet, if we fragment they kill them 
````bash
nmap -p22 IP -f
````
### --mtu
-mtu (maximum transmision unit) if you a put a lower number that the firewall wait you can jump the rule and see the port.
The number have to be a multiple of 8
````bash
nmap -p22 IP -f --mtu 16
````

### -D
To spoof IP, in this case mean that **send SYN with our IP and with the indicate IP**

````bash
sudo nmap -p22 192.168.1.1 -D 192.168.1.200
````

You can put multiples IP:
==Important!!==
It's very useful **when only one IP can see a specific port**
or
They firewall or IDS don't know who IP is making the scan
You have to use nmap **with sudo** if not doesn't work
````bash
sudo nmap --top-ports 500 192.168.1.1 -D 192.168.1.2,192.168.1.3,192.168.1.4,192.168.1.5,192.168.1.6,192.168.1.7,192.168.1.8,192.168.1.9,192.168.1.10,192.168.1.11,192.168.1.12,192.168.1.13,192.168.1.14,192.168.1.15,192.168.1.16,192.168.1.17,192.168.1.18,192.168.1.19,192.168.1.20,192.168.1.21,192.168.1.22,192.168.1.23,192.168.1.24,192.168.1.25,192.168.1.26,192.168.1.27,192.168.1.28,192.168.1.29,192.168.1.30,192.168.1.31,192.168.1.32,192.168.1.33,192.168.1.34,192.168.1.35,192.168.1.36,192.168.1.37,192.168.1.38,192.168.1.39,192.168.1.40,192.168.1.41,192.168.1.42,192.168.1.43,192.168.1.44,192.168.1.45,192.168.1.46,192.168.1.47,192.168.1.48,192.168.1.49,192.168.1.50,192.168.1.51,192.168.1.52,192.168.1.53,192.168.1.54,192.168.1.55,192.168.1.56,192.168.1.57,192.168.1.58,192.168.1.59,192.168.1.60,192.168.1.61,192.168.1.62,192.168.1.63,192.168.1.64,192.168.1.65,192.168.1.66,192.168.1.67,192.168.1.68,192.168.1.69,192.168.1.70,192.168.1.71,192.168.1.72,192.168.1.73,192.168.1.74,192.168.1.75,192.168.1.76,192.168.1.77,192.168.1.78,192.168.1.79,192.168.1.80,192.168.1.81,192.168.1.82,192.168.1.83,192.168.1.84,192.168.1.85,192.168.1.86,192.168.1.87,192.168.1.88,192.168.1.89,192.168.1.90,192.168.1.91,192.168.1.92,192.168.1.93,192.168.1.94,192.168.1.95,192.168.1.96,192.168.1.97,192.168.1.98,192.168.1.99,192.168.1.100 -v -n
````

### Source/origin port
When you send a packet a port i own machine is open (random) temporally and receive the packet in the same port, we can manipulate that.

Imagine that the firewall only admit packet that come from a specific source/origin port
You can use the switch -**-source-port**
````bash
sudo nmap -p22 192.168.1.1 --open -T5 -v -n --source-port 53
````

We can see that this work with wireshark at begin of info is big

### Packet length ( --data-length )
The firewall can reject packets by the length, we can manipulate that:
Only we can **raise** the length

The quantity is add with the original length
````bash
sudo nmap -22 192.168.1.1 --data-length 21
````

### Spoofing MAC

You can put Dell, VMware., manual MAC..

````bash
sudo nmap -p22 192.168.1.1 --spoof-mac Dell
````

You can use this when know that some service are only accesible for many MAC, you can do an [[arp-scan]] and spoof a computer MAC
````bash
sudo nmap -p22 192.168.1.1 --spoof-mac 00:11:22:33:44:55
````

Yes you have an error, they say that the host is down, remember to avoid this we can use the switch -Pn

````bash
sudo nmap -p22 192.168.1.1 --spoof-mac Dell -Pn
````
**Not always this works**,

- `--scan-delay <time>ms` --> add a delay between packet sent.
- `--badsum` --> this generate a invalid checksum for the packets. The firewalls may potentially responds drop this packets. It is useful to detect a firewall
- `--data-length` --> To create a random length of arbitrary packet

In man of nmap you have a lot of paramenters, search by FIREWALL/IDS EVASION AND SPOOFING

### Syn port Scan ( -sS )
Some firewall only register the complete connection and SYN scan when receive a SYN/ACK they send a RST and close.
- This is a faster scan

````bash
nmap -p- --open -sS --min-rate 5000 -v -n -Pn 192.168.1.1
````

`--min-rate` --> Packets not more slowly than 5000 packets of second, take care because if you a high quantity you can see false positive or not see some port

--max-rate also exist


# Webgraphy
- https://hack4u.com
- https://tryhackme.com/room/furthernmap
- [chatGPT](https://chat.openai.com/)
