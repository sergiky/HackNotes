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

# What is Session Puzzling?

When a attacker user a identified of session valid for a user, the attacker share this, if the URL allow this, the attacker can share something like this:

> http://test.com/login.php?session_id=123456

If the user login with the same session_id, you have the same session that the victim and you can access to the same information that the victim.

You have to manipulate the user to persuade then to login in the given url

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

And intercept this with Burpsuite

---

# Labs


````bash
# Build the image
sudo docker pull blabla1337/owasp-skf-lab:sessionpuzzle

# Run the container
sudo docker run -dit -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:sessionpuzzle
````

