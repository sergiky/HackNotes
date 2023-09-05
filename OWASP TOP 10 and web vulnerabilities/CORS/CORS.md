---
tags:
  - vulnerabilty
  - "#cors"
---
# What is CORS?

**Cross-Origin Resource Sharing**

When are different domains, you can realize a request from other domain (imagine that you hacked a domain with an [XSS](../XSS/XSS.md) but you can point to other domain by this)

If a CORS is bad defined you can point from a external domain to other resources from other pages.

By default the policy of **same origin** in this case don't work because as third-party server is not allowed to bring resources 

The policy of CORS define **the third-party domains** that can realize request to take resources, only the domain that you define them, sometimes is bad defined and used wildcards ( like * ) 

---

# Exploiting

We are going to create a malicious page that have an script that allow to see all the content that the user is viewing, this is possible because the CORS is bad configured

## Intercept the request with burpsuite

The credential are admin:admin

![](../../Images/Pasted%20image%2020230905172127.png)


Intercept this with Burpsuite and send to Repeater, in the repeater send the request and in the response we can see the following headers:
````java
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: *
````

We can introduce a header in our request:

````bash
Origin: https.//test.com
````

Send the request and see if in the header Access-Control-Allow-Origin put the URL that indicate

![](../../Images/Pasted%20image%2020230905172517.png)

This allow to that domain to do requests.

This is a big failure, now as a malicious attacker I can control this

We can put some domain that is under our control, or put localhost

![](../../Images/Pasted%20image%2020230905172850.png)



---
# Labs

Build the image:

> sudo docker pull blabla1337/owasp-skf-lab:cors

To launch the image use:

````bash
sudo docker run -ti -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:cors
````


