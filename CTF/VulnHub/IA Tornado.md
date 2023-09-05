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

In this machine (this problem occurs in other machines from VulnHub)

This tutorial is prepared for Oracle Virtual Box.

1. Launch the machine and maintains the key **e** pressed to enter in **edit mode**
2. Once you are in edit mode, go down and search the line that have the word linux and where put **ro quiet**, put **init=/bin/bash**
![](../../Images/Pasted%20image%2020230906002150.png)

![](../../Images/Pasted%20image%2020230906002227.png)
This is to launch a bash at the start of the machine.

3. Ctrl + x to boot the machine
4. 