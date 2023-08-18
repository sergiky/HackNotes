
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

## Exploiting

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



---

# Labs

- [OWASP lab](https://github.com/OWASP/crAPI) (The project is a little unstable, drop all and repeat the installation )

---

# Tags

#vulnerabilty  #api