
# Server-Side Request Forgery

Allow to do request HTTP in server-side to some domain choose by de attacker

Thanks to these we can see some internal website or things that are not expose 

---

# See service in the local machine

Imagine that you have a parameter in url that show the response of the server indicate, you can try to use this from enumerate ports of the server using wfuzz(I think that this is very noisy)

> http://172.17.0.2/utility.php?url=http://127.0.0.1:4646

If there are some resources and do hoverboard you see that the resource corresponds to other IP, you have to **put the service that you find in the url**:

> http://172.17.0.2/utility.php?url=http://127.0.0.1:4646/login.html


---

# See service in machines of the internal network of the enterprise

This works thanks to the first machine because have two interface and one of them is for an internal network, in this internal network could be that some machine are using some service that are not exposes 

> http://172.17.0.2/utility.php?url=http://10.10.0.3:4646/login.html

---

# Tags

----

#ssrf #vulnerabilty #internalportdiscovery