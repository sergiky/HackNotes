---
tags:
  - vulnerabilty
  - "#deserializationpickle"
  - python
---
# What is Pickle?

Is a module for serializing (pickling) and deserializing (unpickling) Python objects

---

# Exploiting

Open the browser localhost:5000

![](../../Images/Pasted%20image%2020230906202717.png)

The data that we put in the form is keeping in a variable named **att**

The problem is that there isn't any type of sanitization, you can see the code in **/python/DES-Pickle/DES-Pickle.py**

![](../../Images/Pasted%20image%2020230906203747.png)

In Pickle there are a function that is named `___reduce__` that you can indicate how do you like to deserialize or what action to do

---

# Labs

> sudo docker pull blabla1337/owasp-skf-lab:des-pickle

> sudo docker run -dit -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:des-pickle