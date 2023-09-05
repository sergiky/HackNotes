---
tags:
  - vulnerabilty
  - "#sqltruncation"
  - sql
---
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


# Labs

- [Tornado Vulnhub](https://www.vulnhub.com/entry/ia-tornado,639/)

