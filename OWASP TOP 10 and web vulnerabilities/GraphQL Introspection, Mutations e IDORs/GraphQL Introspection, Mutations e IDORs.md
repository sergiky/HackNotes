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

You can intercept the request with BurpSuite



---

# Labs

- skf-labs/nodeJs/Graphql-IDOR/

`npm install --force`
`npm start`