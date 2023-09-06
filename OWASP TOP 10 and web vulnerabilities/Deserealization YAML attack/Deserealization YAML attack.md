---
tags:
  - vulnerabilty
  - "#deserializationYAML"
  - python
---
# What is Des-YAML?


---

# Exploiting

![](../../Images/Pasted%20image%2020230906193315.png)

If you look at the URL you see something like that:

> http://localhost:5000/information/eWFtbDogVGhlIGluZm9ybWF0aW9uIHBhZ2UgaXMgc3RpbGwgdW5kZXIgY29uc3RydWN0aW9uLCB1cGRhdGVzIGNvbWluZyBzb29uIQ==

This is a string in base64

If we decode we can see this:

![](../../Images/Pasted%20image%2020230906193547.png)

Mmm, we can try to change the data:

````bash
echo -n "yaml: Hello my nickname is sergiky" | base64
````

You can put this information in the URL

And see this:

![](../../Images/Pasted%20image%2020230906193803.png)

You can see the source code in **DES.py** file

And we don't see any type of validation or sanitization

If there are not validation we can create a special structure that when is serialized and deserealized allow to execute commands  

In the page of [Peter Murphy](https://www.pkmurphy.com.au/isityaml/) 

---

# Labs

[https://github.com/blabla1337/skf-labs/tree/master/python/DES-Yaml](https://github.com/blabla1337/skf-labs/tree/master/python/DES-Yaml)

To execute this you have to use python2.7

````bash
python2.7 DES.py
````

If you have an error with the module yaml install them with pip:

````bash
pip2 pyyaml
````