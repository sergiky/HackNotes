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



---
# Labs

Build the image:

> sudo docker pull blabla1337/owasp-skf-lab:cors

To launch the image use:

````bash
sudo docker run -ti -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:cors
````


