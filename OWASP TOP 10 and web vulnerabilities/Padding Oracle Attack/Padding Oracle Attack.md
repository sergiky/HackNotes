
# Definition

This type of attack try to decipher the the data encrypted without know the key 

- What is an oracle (oraculo in Spanish)?

> When give some information or hint when the action is correct or not

- What is padding?

>  To fill the block of bytes

Imagine that you use [PKCS7](https://en.wikipedia.org/wiki/PKCS_7), this method consist in case of don't fill the total size of the length of that block, auto fill the null bytes to finish the block.

If there are two bytes in null they put 0x02, 0x2, if there are three they put 0x03, 0x03, 0x03...


![](../../../Images/Pasted%20image%2020230803172830.png)

---

There are a type of encryption **AES-CBC** (**C**ipher **B**lock **C**haining) that encrypt our information and grant the authentication

You have more encrypt method, ECB, CFB, OFB, CTR...

> The encrypt must to be symmetric and based in blocks

----

# How to detect them

When the application decrypt the data, the first of all is decrypt the message and then clean the padding.

When the application clean the padding , if we have invalid padding **that desiccate some detected error** like a slow response, don't show result... This mean that we have a padding oracle

----
## Flipping the first bits

How it works by blocks some characters of the cookie will remain the same, you can change some bits, but others remain equals.

Imagine that you register a name that is similar to the authenticate user. (bdmin | admin )

You can try to apply brute force with the intruder of burpsuite, the type of attack will be **bit flipper** (flipping some bits with the objective that generate variants of cookies until find the correct cookie because is very similar)

----
### Intruder

1. Select the cookie like payload
2. Select sniper attack
3. Go to payload
4. In Payload type list, select bit flipper
5. Format of original data --> Literal value
6. Disable the check of URL-encoding characters (according to the case)
7. Start attack!!!!

If this doesn't work you can try to create another user, like cdmin, ddmin...

For this attack you need a lot of patience and see the responses that change the length, one of them can be the good

---

# Automated tool

- [[Padbuster]]

---

# Resource to practice

- [Vulnhub padding oracle machine](https://www.vulnhub.com/?q=padding+oracle)
- [Pentester lab explain](https://pentesterlab.com/exercises/padding_oracle/course)
- [Video explain Oracle Padding Attack](https://www.youtube.com/watch?v=FSOrT0yKIkE) 

----
# Tags

#vulnerabilty #paddingoracleattack #encrypt #cbc #pkcs7 #tools 

----