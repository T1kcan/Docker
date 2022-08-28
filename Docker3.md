Creating an image from running container
docker run -it ubuntu
apt update -y
apt dist-upgrade -y
apt install apache2 -y
/etc/init.d/apache2 status
/etc/init.d/apache2 start
/etc/init.d/apache2 status
apt install vim-nox -y
# answer location and timezone questions
ctrl+p+q
docker ps
# we have made some changes and we don't want to loose them. 
docker help commit
docker commit 322c4ce97722
docker images
# observe that your container image has been created
# but there is no name and tag for the image
docker help tag
docker tag 831892958c23 web_applicaton:v1.0
docker images
docker inspect 831892958c23
# no author or comment provided
docker inspect web_applicaton:v1.0
docker inspect web_applicaton:v1.0 | grep Author
docker inspect web_applicaton:v1.0 | grep Comment
docker commit --author "Tamer Bincan tbincan@gmail.com" --message "This is an image consist of Apache and Vim upon Ubuntu" 322c4ce97722 tbincan/web_application:v1.0
docker run -d -p 8080:80 tbincan/web_applicaton:v1.0
curl localhost:8080
# we havent provided entrypoint:
# observe the web page on your ec2 instance public Ip:8080
# make sure that container is running as we will create another image from running container
docker commit --change='ENTRYPOINT ["apachectl", "-DFOREGROUND"]' 537c479aacf5 tbincan/web_application:v1.1
docker images
docker ps
docker stop 53
docker start -d -p 8080:80 tbincan/web_application:v1.1
# observe the webpage:8080
## Creating image through using Dockerfile
mkdir docker1
cd docker1
nano Dockerfile
---
FROM ubuntu
MAINTAINER Tamer tbincan@gmail.com

# skip prompts
ARG DEBIAN_FRONTEND=noninteractive

# update packages
RUN apt update; apt dist-upgrade -y

# install packages
RUN apt install -y apache2 vim-nox

# set entrypoint
ENTRYPOINT ["apachectl", "-DFOREGROUND"]
---
docker build -t tbincan/web_application:v1.2 .
docker images
docker run -d -p:8080:80 tbincan/web_application:v1.2
# observe two pages simultaneously
docker ps
docker stop $(docker ps -q)
docker ps -a
docker rm $(docker ps -aq)
docker images
docker rmi 8804366a5523
docker rmi 88 76 ec
docker rmi $(docker image ls -q)

# Docker Image Basic Operations
# observe docker registry page and how Dockerfile is created for each image
# create a folder web
cd ~
mkdir web && cd web
# create a file web_app.py
```bash
echo '
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "<h1>Welcome to Sample Web Page</h1>"
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)
' > web_app.py

# create Dockerfile * the firts letter start with capital D 
vi Dockerfile
```
```Dockerfile
FROM ubuntu
RUN apt-get update -y
RUN apt-get install python3 -y
RUN apt-get install python3-pip -y
RUN pip3 install flask
COPY . /web
CMD python3 /web/web_app.py
```
```bash
docker build .
# observe that the image has no tag; just image ID
docker images
docker tag 6cbdb74e2858 welcome:1.0
docker images
docker inspect welcome:1.0
docker run -dit -p 80:80 welcome:1.0
docker ps
docker exec YourContainerID ls
# observe folder web
docker stop YourContainerID
# create a new Dockerfile
# make sure that index.html is located under current directory
vi Dockerfile1
```
```Dockerfile
FROM ubuntu
LABEL Sample Web Page Application
MAINTAINER Tamer Bincan tbincan@gmail.com
ENV MY_CAT=fluffy
RUN apt-get update -y
RUN apt-get install python3 -y
RUN apt-get install python3-pip -y
RUN pip3 install flask
COPY . /web
WORKDIR /web
ADD index.html .
# CMD python3 /web/web_app.py
ENTRYPOINT ["python3", "web_app.py"]
```
```bash
# maintainer info
# env environment variables
# workdir working directory inside container
# copy <source> <dest>
# add <source> <dest>
# add <website> <dest>
# add <compressed file> <dest>
# run mkdir testdir/
# add /home/ec2-user/index.html /var/www/html/index.html
# FROM busybox
# ARG user=someuser
# ARG build_no=1.0
# docker build --build-arg user=what_user build_no=1.1
docker build -t welcome:1.1 -f Dockerfile1 .
docker images
docker ps
docker inspect welcome:1.1
docker run -dit -p 80:80 welcome:1.1
docker exec YourContainerID printenv
docker stop YourContainerID
```
# Difference between ENTRYPOINT & CMD
```bash
vi Dockerfile-sleep
```
```Dockerfile
FROM busybox
ENTRYPOINT ["sleep"]
CMD ["10"]
```
```bash
# entrypoing ["sleep"] # cannot be changed
# cmd ["10"] # can be changed
docker build -t sleep1 -f Dockerfile-sleep .
docker run -it sleep1 
# it will sleep for 10 seconds
docker run sleep1 3
# this time the container will sleep for 3 seconds
docker run -it 9deje 2
# now it will sleep 2 seconds

# make sure that you give below command under web folder
docker build -t "PUT_YOUR_NAME_HERE/webapp:1.0" .
# docker build -t tbincan/webapp:1.0 .
docker image ls
docker run -d --name firstapp -p 80:80 PUT_YOUR_NAME_HERE/webapp:1.0
# docker run -d --name firstapp -p 80:80 tbincan/webapp:1.0
docker ps
# check your public ip of your instance and observe the web page
# Using Docker Registry
docker login
# use your docker registry credentials to login docker hub throuh EC2
docker images
docker push PUT_YOUR_NAME_HERE/webapp:1.0
# observe your docker registry page 
#now your application is ready to be used in all over the world
docker images
# observe the image size
# let's make it more compact
# create another docker file named "Dockerfile-2"
vi Dockerfile2
```
```Dockerfile
FROM python:alpine
RUN pip install flask
COPY . /web
WORKDIR /web
EXPOSE 80
CMD python ./web_app.py
```
```bash
docker build -t "PUT_YOUR_NAME_HERE/webapp:2.0" -f ./Dockerfile2 .
# docker build -t tbincan/webapp:2.0 -f ./Dockerfile2 .
docker image ls
# observe the size differences 
docker run -d --name firstapp2 -p 8080:80 PUT_YOUR_NAME_HERE/webapp:2.0
# docker run -d --name firstapp2 -p 8080:80 tbincan/webapp:2.0
docker push PUT_YOUR_NAME_HERE/webapp:2.0
# observe layer already exists
# observe your docker registry page 
docker stop firstapp firstapp2
docker stop $(docker ps -q)
# create a Dockerfile for nginx
# seach through official docker hub nginx image tag 
vi Dockerfilenginx
```
```Dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
```bash
docker build -t ntest -f Dockerfilenginx .
docker run -dit -p 80:80 ntest
# try to run nginx with volume mapping
docker logs 66
docker logs -f 66
docker stop 66
docker rm $(docker ps -aq)
docker rm -f $(docker ps -aq)
docker system prune 
docker system prune -a
docker ps
docker ps -a
docker images