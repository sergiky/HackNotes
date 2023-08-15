````bash
masscan -p21,22,139,445, -Pn 192.168.0.0/16 
````

Always you have to exapand the CIDR range in case exist subnetting and we can detect other computers 
````bash
sudo masscan -p21,22,139,445,8080,80,443 -Pn 192.168.0.0/16 --rate=10000
````

if your rate **is very high you can loose some computers**, you can down that:
````bash
sudo masscan -p21,22,139,445,8080,80,443 -Pn 192.168.1.0/24 --rate=1000
````

masscan -

``-Pn`` --> Like nmap, to skip the check if the computer are active