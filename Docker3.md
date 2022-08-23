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
docker build -t tbincan/tbincan/web_application:v1.2 .
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
# make sure that you give below command under web folder
docker build -t "PUT_YOUR_NAME_HERE/webapp:1.0" .
docker image ls
docker run -d --name firstapp -p 80:80 PUT_YOUR_NAME_HERE/webapp:1.0
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
docker image ls 
docker run -d --name firstapp2 -p:8080:80 PUT_YOUR_NAME_HERE/webapp:2.0
docker push PUT_YOUR_NAME_HERE/webapp:2.0
# observe your docker registry page 
docker stop firstapp
docker stop $(docker ps -q)
docker rm firstapp
docker rm $(docker ps -aq)
docker system prune 
docker system prune -a