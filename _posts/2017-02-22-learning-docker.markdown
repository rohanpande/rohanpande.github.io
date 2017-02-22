---
layout: post
title:  "Learning Docker"
date:   2017-02-22 14:12:47 +0530
categories: post
img: docker.jpg
categories: [one, two]
color: 1976D2
author: Rohan Pande
---
<br/>
<hr>
### To run docker  
- docker run docker/whalesay cowsay boo

### To see Docker images on system 
- docker images

### Writing a Docker File 

- A Dockerfile is a recipe which describes the files, environment, and commands that make up an image.
- create a new directory mkdir mydockerbuild
- cd mydockerbuild
** Edit a new text file named Dockerfile 
- nano Dockerfile
** Add a FROM statement by copying the following line into the file 
- FROM docker/whalesay:latest
-- the FROM keyword tells Docker which image your image is based on. 
** Add a RUN statement which will install the fortunes program into the image 
- RUN apt-get -y update && apt-get install -y fortunes
** Add a CMD statement which tells the iamge the final command to run afters its environment is setup this command runs fortune -a and sends its output to the cowsay command. 
- CMD /usr/games/fortune -a | cowsay 
** Check your work. Your file should look just like this 
FROM docker/whalesay:latest
RUN apt-get -y update && apt-get install -y fortunes 
CMD /usr/games/fortune -a | cowsay

### Build an image from your docker file 
- docker build -t docker-whale .

### Run your new docker-whale 
- docker images [ this will provide list of all images at local ]
- docker run docker-whale

### Tag and push the image 
 - docker images [ this will provide list of all images available ]
 - Find the image ID for the image to be tagged and pushed
 - docker tag image ID justrohu/image-name:version
 - docker images [ run again to ensure image has been tagged ]
 - docker login [to login into docker.hub.com account ]
 - docker push justrohu/image-name

### Remove Docker Image 
 - docker image remove [ you can refer to an image by its ID or its name ]
 - docker image remove image ID 

### Pull your new image 
 - docker images 
 - remove duplicate images by docker image remove image ID 
 - docker run docker/whalesay 

### To View List of All Containers 
 - docker ps -a

### To view list of Latest created containers including non-running containers 
 - docker ps -l

### To List the networks on docker 
 - docker network ls 

### To Launch docker into the network by default it will launch in bridge network unless customized otherwise 
 - docker run -itd --name=networktest ubuntu

### inspecting the bridge is the easiest way to find containers ip address 
 - docker network inspect bridge

### To disconnect the container from a network 
 - docker network disconnect bridge networktest

### To create your own bridge network 
 - docker network create -d bridge my-bridge-network

### To add containers to a network 
 - docker run -d --net=my-bridge-network --name db training/postgres

### To inspect bridge
 - docker inspect --format='{{json .NetworkSettings.Networks}}' db 

### To add container to network without specifying network 
 - docker run -d --name web training/webapp python app.py

### To get the ip address on which container is deployed 
 - docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

### To open a shell to running container
 - docker exec -it db bash

### To attach a container to other network 
 - docker network connect my-bridge-network web

<hr>

## Data Volumes

### Adding a data volumn 
  - docker run -d -P --name web -v /webapp training/webapp python app.py
  - you can also use the VOLUME instruction in a Dockerfile to add one or more new volumes to any container created from that image. 

### Location a volumn 
 - docker inspect web [ container name ] 

### Mount a host directory as a data volume 
 - docker run -d -P --name web -v /src/webapp:/webapp training/webapp python app.py

### Mount a host directory as data volume with read only 
 - docker run -d -P --name web -v /src/webapp:/webapp:ro training/webapp python app.py

### Mount a host file as a data volume¶
 ** The -v flag can also be used to mount a single file - instead of just directories - from the host machine.
 - docker run --rm -it -v ~/.bash_histroy:/root/.bash_histroy ubuntu /bin/bash

### Creating and mounting a data volume container 
 - docker create -v /dbdata --name dbstore training/postgres /bin/true

### To mount the /dbdata volume in another container by using --volumes-from flag
 - docker run -d --volumes-from dbstore --name db1 training/postgres
 - docker run -d --volumes-from dbstore --name db2 training/postgres

### To extend chain by mounting the volume that came from the dbstore
 - docker run -d --name db3 --volumes-from db1 training/postgres

### Backup, restore or migrate data volumes 
 - docker run --rm --volumes-from dbstore -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata

### To remove volumes 
 - docker run --rm -v /foot -v awesome:/bar busybox top

### To delete containers 
  - docker rm -f <container>

### Delete all available containers
  -	docker rm -f $(docker ps -aq) 
 
### To expose default ports 
  - docker run -tid -P  --name apache_server nimmis/apache-php5

### Specify a different post <host port>:<container port>
  - docker run -tid -p 80:80 --name apache_server  nimmis/apache-php5 

### Example
  - docker run -tid -p 80:80 --name="apache_server" -v /Users/admin/Desktop/www/TEST:/var/www nimmis/apache-php5

### Dockerfile example 
 
 FROM justrohu/apache-php5
 MAINTAINER justrohu <justrohu@gmail.com> 
 COPY 000-default.conf /etc/apache2/sites-available/000-default.conf  [ overridding container httpd.conf with host conf ]

 EXPOSE 80    # exposing http port
 EXPOSE 443   # exposing https port

### CMD ["executable","param1","param2"] { syntax on command writting }

 CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

 RUN apt-get update && \ apt-get install nodejs && \ apt-get install npm

 ENV MYSQL_ROOT_PASSWORD=root
 ENV MYSQL_ROOT_USER=root

### To Stop a running container 
 - docker stop <container name>