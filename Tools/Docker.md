# Installation and run service

````bash
sudo apt install docker.io -y
````

Run the daemon
````bash
service docker start
````

-------

## ¿What is an image/Dockerfile?
It is a file that contains all the necessary that permit execute a program in a container
It's like a model that contains the necessary files, libs...

---------

### To show information about images
````bash
sudo docker images
````

------

### To see all the ID of the images with \\n
````bash
docker images -q 
````

------

#### Common Syntax

- **FROM**: Indicate the base image where create the new image.
- **RUN**: Indicate commands inside the containers, like installation of dependencies, programs...
- **COPY**: To copy files to the host to the container.
- **ENTRYPOINT**: Indicate the command that want to execute where run the container.
- **EXPOSE PORT**: Expose the port indicate in the container, like 80,21,22,443,445...

-----

### Examples of Dockerfiles
````docker
FROM ubuntu:latest

MAINTAINER Sergio Guijarro aka sergiky
````

`FROM ubuntu:latest` --> Indicate that the container have a Ubuntu and you can indicate the version or use the tags, one of them are latest

`MAINTAINER Sergio Guijarro aka sergiky` --> Give some information about who maintains this container

Other example:
````bash
FROM ubuntu:latest

MAINTAINER Sergio Guijarro aka sergiky

ENV DEBIAN_FRONTEND noninteractive

RUN apt update && apt install -y net-tools \
    iputils-ping \
    curl \
    git \
    nano \
    apache2 \
    php

EXPOSE 80

COPY prueba.txt /var/www/html

ENTRYPOINT service apache2 start && /bin/bash
````

We're installing updating the system and installing necessary tools.
The `\` is only to put the tools tabulated in the Dockerfile.


``ENTRYPOINT service apache2 start && /bin/bash`` --> This part is to launch the apache2 when container start and the **&& /bin/bash** is for solution a problem when launch the container that the bash don't off and maintain active
`

Remember you have to execute again the **docker build command**
````bash
sudo docker build -t my_first_image:v2 .
````

It's the same name but **changing the tag**

------

#### Execute the model (docker build)

Command to mountain the container with the specific options indicate in the file

````docker
docker build -t my_first_image .
````
The name have to be in lowercase
`.` --> Search the file Dockerfile in the actually directory

----

#### Docker pull
Permit download images in a register of docker

You can use:
````bash
sudo docker pull debian:latest
````

---------

### Create container

````bash
sudo docker run -dit --name myContainer my_first_image
````
This show the id of our container

`d` --> --detach. To have the container in bacgrkound
`i` --> --interactive. Interactive
`t` --> --tty. To have a virtual terminal

----------

### ¿How Can I see if there are some container running?
You can use this command:
````bash
sudo docker ps
````

You can use the parameter:
`-a o --all` --> To list all the containers, including those who stand
`-q o --quiet` --> Only show the ID of the containers 

----------

#### Only show the ID of the containers
````bash
sudo docker ps -a -q
````

--------

### Connect to the container
````bash
sudo docker exec -it myContainer bash
````
You can use the name of the container or the ID to launch them
In place of bash you can use other command like whoami, id...

`-i` --> interactive
`-t` --> To indicate the terminal

----------

### How to stop a container?

````bash
sudo docker stop ID
````

-------

## How to delete a container

````bash
sudo docker rm ID
````

When a container is runnging you have to delete using the param --force
````bash
sudo docker rm ID --force
````

--------

### Way to delete all the containers
````bash
sudo docker rm $(sudo docker ps -aq)
````

---------

## How to delete images
Remember if the image have image child you have to delete them
````bash
sudo docker rmi ID
````

------

### How to delete all the images
````bash
sudo docker rmi $(sudo docker images -aq) 
````

--------

## Port Forwarding
The first of all, in the Dockerfile you have to expose the port
````bash
EXPOSE 80
````

Then you have to build them
````bash
sudo docker build -t webserver .
````

-----

## Filter by images that contain none
This image doesn't necessary we can delete
````bash
sudo docker images --filter "dangling=true" -q
````

`-q` --> To obtain the ID

-----

## Mode interactive when installing apache2
You can set a environment variable in Dockerfile to solution this
````bash
ENV DEBIAN_FRONTEND noninteractive
````

Re-build the images

And now create the container:
````bash
sudo docker run -dit -p 80:80 --name myWebServer webserver
````
Look, you are indicating that the port of your host (80) is the port of the container (80)

--------

## How to see the port forwarding of the container
You can use this command
````bash
sudo docker port NAME
````

You can check if your port is used with **lsof -i**
Now if you open the browser and put **localhost** you can see the webServer but this is working in docker container

--------

## Mounts
Like share folders in Oracle VirtualBox
````bash
sudo docker run -dit -p 80:80 -v /home/sergio/Desktop/Academia/Docker/:/var/www/html/ --name myWebServer webserver
````
Look the param **-v** that means **volume**

-------

### How to see logs
````bash
sudo docker logs ID
````

With param **-f** you can see incoming request

---------

### Volums
It's like cache, when you reinstall the container you have the same configuration that the last time

To see there you can use
````bash
sudo docker volume ls
````

To output only the values
````bash
sudo docker voulme ls -q
````

To delete all
````bash
sudo docker volume rm $(sudo docker volume ls -q)
````

---

# Networks in docker

To see the created network:

> sudo docker network ls

Create a new network:

> sudo docker network create --driver=bridge network1 --subnet=10.10.0.0/24

``--driver`` --> You have different modes like bridge, host, null
``network1`` --> Is the name of the interface
`--subnet` --> The range of ip to use

## Create a new interface and add to a container

> sudo docker network connect network1 PRO

`network1` --> The name of the network
`PRO` --> The name of the docker container

## To run a new container with the new interface

> sudo docker run -dit --name PRE --network=network1 ubuntu

`--network` --> With the parameter network

-----

# Iptables in docker

If you want to use [[Iptables]] in docker you have to add this when you use the command **docker run**

> --cap-add=NET_ADMIN

------

# Tags

#docker 

----