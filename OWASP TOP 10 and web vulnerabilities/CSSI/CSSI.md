---
tags:
  - vulnerabilty
  - "#cssi"
  - xss
---

# CSS Injections

Some part in the website allow you to insert, inject some code that are going to use in css

![](../../Images/Pasted%20image%2020230906190856.png)

In this case if you put a color and see the source code you can see this:

![](../../Images/Pasted%20image%2020230906191000.png)

# CSSI to XSS

What happen if close the curly brace and then the `</style>` tag

````html
red}</style><script>alert("CSSI to XSS")</script>
````

![](../../Images/Pasted%20image%2020230906191425.png)


---

# Labs

- [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/CSSI](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/CSSI)