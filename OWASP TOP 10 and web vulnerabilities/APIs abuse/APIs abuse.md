
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

---

# Labs

- [OWASP lab](https://github.com/OWASP/crAPI) (The project is a little unstable, drop all and repeat the )

---

# Tags

#vulnerabilty  #api