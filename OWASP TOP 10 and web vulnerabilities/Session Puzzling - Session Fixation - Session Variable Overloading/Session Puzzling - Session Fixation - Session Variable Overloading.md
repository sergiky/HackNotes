---
tags:
  - vulnerabilty
  - "#sessionfixation"
  - "#sessionvariableoverloading"
  - "#sessionpuzzling"
---

# What are Session Puzzling, Session Fixation y Session Variable Overloading?

This vulnerabilities allow to the control of the session in a web application


---

# What is Session Fixation?

When a attacker user a identified of session valid for a user, the attacker share this, if the URL allow this, the attacker can share something like this:

> http://test.com/login.php?session_id=123456

If the user login with the same session_id, you have the same session that the victim and you can access to the same information that the victim.

You have to manipulate the user to persuade then to login in the given url

---

# What is Session Puzzling?

Is the same vulnerability but the attacker have to try to find or generate session id valid

---

# Session Variable Overloading

This type of attack (comes from Session Fixation) an attacker send a lot of data in the web application with the objective of overload the session variables. If the web application not validate successfully the amount of data, the attacker can overload with malicious data or some problems of performance

---

# Ways to avoid this

Using random secures and random session, validate the authentication and limit the data of a session variable

---

# Exploiting

In this case this lab is more similar to a cookie manipulator that a session Puzzling

If you open the Storage tool (in developer tools of the explorer) you can see the session cookie, that have the format of  JSON web token (because have the format header.payload.signature)

JSON web token:

````bash
eyJsb2ciOnRydWUsInVzZXJuYW1lIjoiYWRtaW4ifQ.ZPhZ6w.iRQu0maCAXQ77l5dsG84ykbrm90
````

We have a the website [jwt.io](https://jwt.io) to decode this:

![](../../Images/Pasted%20image%2020230906125624.png)

In the login, you can click in **Forgot Password**? 

![](../../Images/Pasted%20image%2020230906130743.png)


![](../../Images/Pasted%20image%2020230906131128.png)

Now we are going to use Burpsuite and intercept this, send to repeater.

You can see a Cookie-Session in the response:

![](../../Images/Pasted%20image%2020230906131554.png)

If we [jwt.io] we can see that is the same, they give as the session cookie, now if you change the URL and put **dashboard** you can see the content

> http://localhost:5000/dashboard

This is because we have the **Set-Cookie**

![](../../Images/Pasted%20image%2020230906131759.png)


---

# Labs


````bash
# Build the image
sudo docker pull blabla1337/owasp-skf-lab:sessionpuzzle

# Run the container
sudo docker run -dit -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:sessionpuzzle
````

