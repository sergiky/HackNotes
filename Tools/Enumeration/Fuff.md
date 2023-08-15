Like wfuzz but this is writing in go.
Ffuff is more faster than wfuzz because go work better with connections and sockets
````bash
ffuf -c -t 200 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u https://miwifi.com/FUZZ/ --mc=200
````

`mc` --> Match a status code that you indicate

`-v` --> (verbose) This param say where the redirect go or some more information