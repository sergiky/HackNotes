
# Definition

Allow an attacker to redirect the users to malicious website.

If we can modified the parameter of the redirect, we can change the URL 

Normally this vulnerability is using for phishing
Or you can aim to a internal service of a server and use an [XSS](../XSS/XSS.md) attack to an admin that click in the URL

If you concatenate this vulnerability with others can be dangerous

---

# Exploiting

Imagine that you have an url like this:

> localhost:5000/redirect?newurl=/newsite


If you can change the /newsite to other URL this is consider a Open Redirect

> localhost:5000/redirect?newurl=https://google.es

## Avoid the point prohibition

Imagine that in the website there are a validation and don't allow to put a point in the url, in the last case google.es have a point

To avoid this you can url encode the point, but the server url-decode the point and is in the same

> /redirect?newurl=https://google%2ees

You can url-encode the **%**

> https://google%252ees

![](../../Images/Pasted%20image%2020230824181057.png)

And yep, this work

## Avoid slash prohibition

The developers may have programmed that you can use a slash ( / ) 

You can do the same, a double URL-encode, but this way **doesn't work**

**Trick!!** In https website you don't need to put the double slash

> https:google%252ees

---

# Labs

- [Open Redirect 1](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection)
- [Open Redirect 2]([https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder))
- [Open Redirect 3]([https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder2](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder2))



---

# Tags

#vulnerabilty #openredirect