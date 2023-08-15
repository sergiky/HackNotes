
# Passive way

This form don't do any type of noise

## CTFR github | Abusing SSL Certificate transparency
You can add this repository in your opt folder and install the requirements
````bash
pip3 install -r requirements.txt
````

To execute the program you have to put:
````bash
python3 ctfr.py -d tinder.com
````

## sublist3r | use OSINT

We have to download and install

````bash
git clone https://github.com/aboul3la/Sublist3r.git
````

````bash
python3 setup.py install
````

Is very easy to use:
```bash
python3 sublist3r.py -d tinder.com
```

# Active way
## gobuster
Go is a very good language to work with socket and connections
You're going to use a bruteforce attack

````bash
gobuster vhost -u https://tinder.com -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 20 | grep -v "403"
````

`-vhost` --> It's a mode that allow to discovery new subdomain
`-u` --> Indicate the URL
`-w` --> Indicate the wordlist
`-t` --> threads
`grep -v` --> To see the lines that don't contain the word "403"

## wfuzz

To fuzzing rutes, param, request get and post, extension, multiple field tu fuzzing

````bash
wfuzz -c --hc=403 -t 20 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.tinder.com" https://tinder.com
````

`-c` --> Indicate that the output have colors
`--hc` --> Don't show the line that have a 403 code in the response
`-t` --> Indicate the threads
`-w` --> indicate the wordlist
`FUZZ` --> Is where de you like to put your payload

## Additional Tools

https://phonebook.cz/ -- free website