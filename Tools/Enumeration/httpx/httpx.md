

````bash
httpx -sc -cl -title -l tesla.txt > tesla_domains.txt
````

You can use other utils:

````bash
cat tesla_domains.txt | httpx -sc -cl -location -title | anew httpx.txt
````

---
# Tags
