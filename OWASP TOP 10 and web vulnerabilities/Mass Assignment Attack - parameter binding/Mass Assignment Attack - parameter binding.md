
# What is an Assignment Attack

This attack add new values in our request that we can see in the response.
Imagine that you register an user and see in the response that there are some values that not stay in the request, you can try to add this in our request

---

# Exploiting

They repo build the image, but we have to run them:

> sudo docker run -dit -p 3000:3000 --name JuiceShop bkimminich/juice-shop

We launch in our explorer, and we are going to intercept the request of register an a customer (drop the request and send in the **Repeater**) 

If you look up, in the response have more values that are sending in the request

![](../../Images/Pasted%20image%2020230823182901.png)

You can see that are one that is **role**, you can try to add **"role":"admin"** in our request

You have to change the email because there are an account with this mail created

![](../../Images/Pasted%20image%2020230823183105.png)

If you go to the website, there are a new message

![](../../Images/Pasted%20image%2020230823183313.png)


---
# Labs

- https://hub.docker.com/r/bkimminich/juice-shop


---

# Tags

#vulnerabilty 