---
tags:
  - vulnerabilty
  - "#racecondition"
---

# What is a Race Condition

Where two or more services fight for the same resources and don't exists any mechanism that synchronize the access of services.

If two process try to access at the same time, could be that one of them will be unpredictable, or a not expected behavior 



---
# Exploiting

Go to indicate folder and use `sudo npm install` and `sudo npm start`

If you have some problema with `npm install` add a `--force`

![](../../Images/Pasted%20image%2020230906154654.png)

We are analyze the code of the website that is in **app.js** 

And we can see that we can execute command thanks to this regex

![](../../Images/Pasted%20image%2020230906161006.png)



---

# Labs

We are going to use [skf labs](https://github.com/blabla1337/skf-labs)

nodeJs/RaceCondition

After we are going to see nodeJs/RaceCondition-file-write

