# Client-Side Template Injection

This vulnerability allow to execute code in the browser, normally this vuln is derivative to[[XSS]]

----

# Detect a CSTI

To detect the exploit is equal that an [[SSTI]], you can try with **{{7\*7}}**

---

# Exploit it

We need to know the service (like Angular) and the version, we can find that in the source code of the website.

One time you have to search the injectable code 

## Bypass

Sometimes when you try to insert letters doesn't work, you change the characters to decimal numbers:

````python
python3 # Enter in interactive mode

sentence = "PWNED"
for character in sentence:
	print(ord(character))


````

Then put the numbers like this: 80,87,78,69,68

Now you can use the function:

> String.fromCharCode(80,87,78,69,68)

To convert the numbers in characters

---

# Tags

#csti #vulnerabilty 

----