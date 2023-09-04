---
tags:
  - vulnerabilty
  - "#idors"
---
# What are IDORs?

**Insecure Direct Object References**

This vulnerability is very common, consist in change some identifier that are no posible to indicate in the website, but is able in URL, example:

In this case we have a list with only five types of coffee

![](../../Images/Pasted%20image%2020230904192056.png)

If you look up in the URL, you can see that the id is indicate them, if you try to change the number, for example **ten** you have to see another type of coffee that is not available

> http://192.168.1.70/xvwa/vulnerabilities/idor/?item=1

# Exploiting 

We are going to use [xvwa](../../CTF/VulnHub/xvwa.md) 