# What is a WebDAV

**Web Distributed Authoring and Versioning**
Is a protocol that allow you to save files, modified or shared in a web server

---

![](../../Images/Pasted%20image%2020230828172100.png)

If you realiza a whatweb, you can see the WWW-Authenticate
![](../../Images/Pasted%20image%2020230828174035.png)

We can use [davtest](../../Tools/davtest.md), this tool is used when we found creds to upload many files with php extensions, jsp...

And they say what files can execute (this is util to upload a webshell or reverse shell...)

````bash
davtest -url http://127.0.0.1 -auth admin:admin 2>&1
````

We are going to convert stder to stdout to filter by this.

To check what is the correct password we can use this **oneliner** to bruteforce the password:

````bash
 cat /usr/share/wordlists/rockyou.txt | while read password; do response=$(davtest -url http://127.0.0.1 -auth admin:$password 2>&1 | grep -i succeed); if [ $response ]; then echo "La contraseña correcta es $password"; break; fi; done
````

This is very slow, you can play with threads.

There are other tool that is named [cadaver](../../Tools/cadaver.md)

````bash
cadaver http://127.0.0.1
````

This request you the user and password and is like an **ftp interactive session**

You can upload files and will be interpreted (you can upload a reverse shell)

> localhost/pwned/cmd.php

or

> localhost/cmd.php

----
# Labs

- docker pull bytemark/webdav


---

# Tags

#vulnerabilty #webdav