Writing in python
````bash
wfuzz -c -t 200 --hc=404,403 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ/
````

`-c` --> To show the output with colors.
`-t` --> Indicate the threads
`--hc=X` --> To don't show the rutes with the indicate status code
``-L`` --> To follow the redirect

### Hide and show rutes with determinate lines

`--sl=X` --> To return only rutes that have x quantity of lines
``--hl=X`` --> To hide the lines that have the lines indicate

### Hite and show rutes with determinate words
`--hw` --> hide words
`sw` --> show words

### Hide and show characters
`hh` --> hide characters
`sh` --> show characters

# Payloads
### Dictionary payload (-w/-z)
In wfuzz have different payload to do the attack in this case, with w or -z file is a attack by dictionary
````bash
wfuzz -c -t 200 --hc=404,403 -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt https://miwifi.com/FUZZ/
````

### List payload
This is very useful when you want to try to use many extensions

````bash
wfuzz -c -t 200 --hc=404,403  -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -z list,html-php-txt https://miwifi.com/FUZZ.FUZ2Z
````

This is very important, if you want to use other payload like this example (-w and -z) when you refer at this list you have to put **FUZ2Z**, in case you have another payload you use FUZ3Z...

`-z list` --> A list are words separate by hyphen(-)

### Range Payload

````bash
wfuzz -c -t 200 -z range,1-20000 'https://www.mi.com/shop/buy/detail?`roduct_id=FUZZ`'
````