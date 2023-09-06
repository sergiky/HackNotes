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



---

# Labs

We are going to use [skf labs](https://github.com/blabla1337/skf-labs)

nodeJs/RaceCondition

After we are going to see nodeJs/RaceCondition-file-write

