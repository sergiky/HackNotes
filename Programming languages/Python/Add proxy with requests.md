
We can add a proxy (like burpsuite to see all the request that our script are doing)

Remember that you have to **enable the intercept**

````python
import requests

burp = {'http':'http://127.0.0.1:8080'}

r = requests.post(... proxies=burp)

````

----

# Tags

#language #python #proxy 