---
tags:
  - machine
  - gnu/linux
  - vulnhub
---


![](../../Images/Tornado%201.png)
- SQL Truncate


---

# Changing the name of the interface

this problem occurs in other machines from VulnHub

This tutorial is prepared for Oracle Virtual Box.

1. Launch the machine and maintains the key **e** pressed to enter in **edit mode**
2. Once you are in edit mode, go down and search the line that have the word linux and where put **ro quiet**, put **init=/bin/bash**
![](../../Images/Pasted%20image%2020230906002150.png)

![](../../Images/Pasted%20image%2020230906002227.png)
This is to launch a bash at the start of the machine.

3. Ctrl + x to boot the machine
4. Use enter and you have a bash
![](../../Images/Pasted%20image%2020230906002523.png)

5. Now if you see the name of the interface (with **ip a** command) this name of interface is not properly because DHCP doesn't go to give an ip address.
6. Go to /etc/network and open with nano the file **interfaces**
**Important Tip** --> If you can't edit the file and you are root user, you have to remove the immutable attribute



lsattr interfaces

You can use the next command


> chattr -i interfaces

# IMPORTANT

Machine without finished because we can't change the name adapter, more information in [SQL Truncation](../../OWASP%20TOP%2010%20and%20web%20vulnerabilities/SQL%20Truncation/SQL%20Truncation.md)
