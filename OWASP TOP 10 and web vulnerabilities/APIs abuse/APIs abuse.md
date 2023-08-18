
# What is a API

Is a part of code that allow to share information and functionality. Is a intermediary between two systems, one request the data and the other give them.

## What is an endpoint?

It is a entry point that allow to the developers to send request and receive the response of the API, the format of an endpoint be like:

> https://api.example.com/v1/users

In the section of network in the developer tool of firefox, we can filter the request by **XHR (XMLHttpRequest) **

And if we do hoverboard in the requests we can see the **url of the api**

![](../../Images/Pasted%20image%2020230818182428.png)

If we click in the request, we can see more information, like the method (that is POST)

![](../../Images/Pasted%20image%2020230818182644.png)

You can use [Postman](../../Tools/API/Postman.md) to recollect all the endpoint

Now we are going to create a collection, and after **new** and HTTP request and put the url and indicate the method (POST), send this.

You are going to see an error, this is because missing fields,

You are going to go to body, click on the bottom raw, put json  and paste the data extracted from the request (in this case the email and password)

![](../../Images/Pasted%20image%2020230818190247.png)

![](../../Images/Pasted%20image%2020230818190307.png)

And now, if you send the request, you can see the data

Save the request

We add a second request, and give an error, that error reclaim the jsontoken, we can create **variable** in the collection, we add a variable and add the jsontoken

![](../../Images/Pasted%20image%2020230818191143.png)

in the Authetication tab we have to indicate that is a **Beared Token**

You have to indicate the jsonToken, but since we have the variable created, we can invoke theme using {{accessToken}} and then save them

Now, the header of authorization is going to put in all the request

![](../../Images/Pasted%20image%2020230818191553.png)

You can recolate a lot of endpoint and put here to organize 

---

# Exploiting

### Brute-forcing OTP 

In the field of **forgot password**, you can send a email to corespondent account to reset the password, you can see the mails in `localhost:8025`.

You can see the format, in this case only need the OTP and the new password

![](../../Images/Pasted%20image%2020230818202651.png)

You can use the network section in developer option to see the request

![](../../Images/Pasted%20image%2020230818203101.png)

![](../../Images/Pasted%20image%2020230818203211.png)

We are going to represent this url in [Postman](../../Tools/API/Postman.md)

![](../../Images/Pasted%20image%2020230818203403.png)

We can use [Ffuf](../../Tools/Enumeration/Ffuf.md)

> ffuf -u http://localhost:8888/identity/api/auth/v2/check-otp -w /usr/share/seclists/Fuzzing/4-digits-0000-9999.txt -X POST -d '{"email":"sergiky@sergiky.com","otp":"FUZZ","password":"1aA$aaaa"}' -H "Content-Type: application/json" -p 1 -mc 200

´-p 1´ --> 1 second after a request

But the website identify that we are fuzzing theme and block our request

Go to postman and send some requests

![](../../Images/Pasted%20image%2020230818204327.png)

But you can try if exists other version that don't block our attack

> http://localhost:8888/identity/api/auth/v3/check-otp

to

> http://localhost:8888/identity/api/auth/v2/check-otp

we can fuzz again but in this url

The v2 of the API don't block to this bruteforce attack

This is very important, when the **developers don't eliminate a old version of the API** (vulnerable version)

![](../../Images/Pasted%20image%2020230818204639.png)

The OTP code have an expiration time (like 3 minutes more or less) we **resend the code**.

We repeat the process, and fuzz the new url, and **boomb!!**

![](../../Images/Pasted%20image%2020230818205745.png)

The password has changed, now we can login with the new password (1aA$aaaa)

----

### Replacing method

We can try with others method, in this case works with GET, but we can use a dictionary to find others available methods

> ffuf -u http://localhost:8888/workshop/api/shop/products -w /usr/share/seclists/Fuzzing/http-request-methods.txt -X FUZZ -p 1 -mc 401,200

![](../../Images/Pasted%20image%2020230819004702.png)

In this case GET and OPTIONS discard them, OPTIONS method is used to see whats methods are allowed, you can see this in the headers

![](../../Images/Pasted%20image%2020230819004911.png)

It is better to do this request that bruteforce with a dictionary, make less noise

We see that accept method POST

![](../../Images/Pasted%20image%2020230819005152.png)

!!!! Remember if you logout and login again, **the json web token change**, you have to change the value of the variable

But require this three fields, we can fill them in our body section, it looks like we can add a new product?, name, price and the image?

We are going to realice the request

![](../../Images/Pasted%20image%2020230819005958.png)

> {"name":"Hacked","price":"-10000","image_url":"https://static.vecteezy.com/system/resources/previews/003/188/635/original/a-hotdog-sticker-on-white-background-free-vector.jpg"}

Why I put negative number? because this cause that own quantity increase instead of decreasing

![](../../Images/Pasted%20image%2020230819010347.png)

![](../../Images/Pasted%20image%2020230819010356.png)

This is a [Mass Assignment Attack](../Mass%20Assignment%20Attack/Mass%20Assignment%20Attack.md) 

And about the money, we can buy the product we can see how the money raise

![](../../Images/Pasted%20image%2020230819010622.png)

![](../../Images/Pasted%20image%2020230819010754.png)

In postman, we take and enpoint (buy product) that can select what item and how many items do we want

![](../../Images/Pasted%20image%2020230819011002.png)
![](../../Images/Pasted%20image%2020230819011011.png)

---

## Coupon - NoSQL 

With the network tool we can take the endpoint of the coupon

> http://localhost:8888/community/api/v2/coupon/validate-coupon

We can add to our collection in [Postman](../../Tools/API/Postman.md) 

After add the content in body

![](../../Images/Pasted%20image%2020230819012657.png)

In this field, we can try to play with other vulnerabilities, in this case are going to be **NoSQL Injection**

> {"coupon_code": {"$ne":"1"}}

And if it work, you can see the result

![](../../Images/Pasted%20image%2020230819013058.png)

## Vehicle - BOLA

**Broken Object Level Authorization**

![](../../Images/Pasted%20image%2020230819013952.png)

If we can see the vehicule of other proprietary this is consider a BOLA



![](../../Images/Pasted%20image%2020230819013823.png)

We add the endpoint in postman

If you go to community, there are other users, if we use network tool and click in a user message, we can see extra information 

![](../../Images/Pasted%20image%2020230819014421.png)

We can see the id of the other user...

> 10c08d7a-4777-4f37-9fe2-9437b5fc14a3

If we put this ID in the request that is possible to happen a BOLA

![](../../Images/Pasted%20image%2020230819014713.png)

And yes, the BOLA attack works

![](../../Images/Pasted%20image%2020230819014741.png)

Remember in the last request that we see the email, and we found a vulnerabilty abusing the v2 version of the API to bruteforce OTP code

---

# Labs

- [OWASP lab](https://github.com/OWASP/crAPI) (The project is a little unstable, drop all and repeat the installation )

---

# Tags

#vulnerabilty  #api #nosql #bola 