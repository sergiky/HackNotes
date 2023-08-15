````bash
gobuster dir -u https://miwifi.com/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 --add-slash -b 403,404 -x php,html,txt
````
`--add-slash` --> This param put a slash (/) at the end of the **URL**, we include this because some URL redirect to the directory with a slash at final
``-b`` --> Don't show the 404 code by default, you can indicate other status code, but this obligate to put to hide the code 404 or gobuster don't run


````bash
gobuster dir -u https://miwifi.com/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 -b 403,404 -x php,html,txt
````

Remember, you have to remove the --add-slash because if not the route would be like this: https://website/url/index.html/ and this **doesn't work**
`-x` --> to apply fuzzing putting the extensions that indicate. This is very useful to search files.

If you want to see only status code 200 you can use `-s`
````bash
gobuster dir -u https://miwifi.com/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 -s 200 -b "" -x php,html,txt
````
`-s` --> Positive string, if you have an error you have to put the blacklist empty using **-b ""**

`--append-domain` --> 