---
tags:
  - vulnerabilty
  - "#sqltruncation"
  - sql
---
# IMPORTANT

I can't finish the class because the machine don't allow to me to change the name of network adapter

![](../../Images/Pasted%20image%2020230906011351.png)

# What is SQL Truncation?

Imagine that you a register form in a website and you put a email that is already in use, if the max of character is not reachable you can add something like that:

````bash
admin@admin.com  a
````

Now imagine that you exceed the limit of characters by 1, then delete the **a character** and maintain the mail:

````bash
admin@admin[space][space]
````

What happens with the spaces? The spaces are not consider like value information, then truncate them (delete the spaces), after this you should be able to change the password of the "new user"

# Exploiting

We are going to use [IA Tornado](../../CTF/VulnHub/IA%20Tornado.md) 



# Labs

- [Tornado Vulnhub](https://www.vulnhub.com/entry/ia-tornado,639/)

