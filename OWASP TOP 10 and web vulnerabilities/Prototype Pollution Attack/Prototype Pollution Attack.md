
# Definition

This technique allow to an attacker to execute JavaScript code modified the property "**prototype**"

This property is used to define the properties and method of an object

---

When there are a merge of objects in the source code, may arise the prototype pollution

If a property doesn't exists (undefined) this see the prototype, that is where inherit some properties.

![](../../Images/Pasted%20image%2020230823002240.png)

In Burpsuite, you can add a new field called proto:

````bash
__proto__:"isAdmin": true
````
 
The objects that don't contain the property isAdmin inherits, pollute the object

---

# Explotation

We try to see a json structure, to this change the content-type:

> Content-Type: application/json

And change the data

![](../../Images/Pasted%20image%2020230823003530.png)

Now, you can put the `__proto__` with the field "admin":true

We know that the name of the property is admin because we read in the source code, you can try to do bruteforce but is not recommendable

![](../../Images/Pasted%20image%2020230823003713.png)

Reload the webpage

![](../../Images/Pasted%20image%2020230823003844.png)



---

# Tags

#prototypepollution