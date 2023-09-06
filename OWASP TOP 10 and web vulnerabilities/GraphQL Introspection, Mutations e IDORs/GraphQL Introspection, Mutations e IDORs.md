---
tags:
  - vulnerabilty
  - "#graphql"
  - "#introspection"
  - "#mutations"
  - idors
---
# What is GraphQL

Is a query language used to manipulate data with different APIs

---

# Exploiting

**localhost:5000**

![](../../Images/Pasted%20image%2020230906221959.png)

The credentials are **johndoe** and **password1**

![](../../Images/Pasted%20image%2020230906222109.png)

You can try to fuzz the website with [Gobuster](../../Tools/Enumeration/Gobuster.md):

````bash
gobuster dir -u http://localhost:5000 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
````

You will found a **settings**
![](../../Images/Pasted%20image%2020230906222311.png)

You can find this in "settings":

![](../../Images/Pasted%20image%2020230906222336.png)

You can intercept the request with BurpSuite and click on forward and you have to see this request
![](../../Images/Pasted%20image%2020230906222548.png)

Okay, send that to the **Repeater**

If you visit the page:

> http://localhost:5000/graphql

You see like a terminal, all the data that is sending to this are queries that obtain the result and return that.

If you see the request, they indicate that the id of the user is **1** and request more data about this user

![](../../Images/Pasted%20image%2020230906222857.png)

And in response we obtain the data

![](../../Images/Pasted%20image%2020230906222923.png)

And yes, if you are thinking about to apply IDOR in id user you are on the right track

If you change the ID and put 2 in place of 1 you see other response:

![](../../Images/Pasted%20image%2020230906223115.png)
![](../../Images/Pasted%20image%2020230906223121.png)


---

# Labs

- skf-labs/nodeJs/Graphql-IDOR/

`npm install --force`
`npm start`