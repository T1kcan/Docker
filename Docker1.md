```bash
sudo yum update -y
```
# Install the most recent Docker Community Edition package.
```bash
sudo amazon-linux-extras install docker -y
```
#  Start docker service.
Init System: Init (short for initialization) is the first process started during booting of the computer system. It is a daemon process that continues running until the system is shut down. It also controls services at the background. For starting docker service, init system should be informed.
```bash
sudo systemctl start docker
# Enable docker service so that docker service can restart automatically after reboots.
sudo systemctl enable docker
# Check if the docker service is up and running.
sudo systemctl status docker
# Add the `ec2-user` to the `docker` group to run docker commands without using `sudo`.
sudo usermod -a -G docker ec2-user
# Normally, the user needs to re-login into bash shell for the group `docker` to be effective, but `newgrp` command can be used activate `docker` group for `ec2-user`, not to re-login into bash shell.
newgrp docker
# Check the docker version without `sudo`.
docker version
# Check the docker info without `sudo`.
docker info
```
# Installing Docker on Ubuntu
```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic test"
sudo apt install docker-ce
sudo groupadd docker #Create the docker group if it does not exist
sudo usermod -aG docker $USER #Add your user to the docker group
#Run the following command or Logout and login again and run
newgrp docker
sudo snap install docker
```
# Docker Help
```bash
docker help | less
docker help run | more
```
# Running Containers
```bash
docker pull hello-world
docker run hello-world
docker run -i -t ubuntu /bin/bash
hostname
# Display the shell name on the container for the current user.
cat /etc/os-release
# show which shell is being used
echo $0 
# observe that containers share the same kernel with hosting docker server
uname -a
#Linux 101346efe38e 5.10.130-118.517.amzn2.x86_64 #1 SMP Wed Jul 13 16:51:52 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
apt-get update && apt-get install git
exit
# docker ps shows us the list of running containers.
docker ps
# When we specify the -a flag, the docker ps -a command will show us all containers, both stopped and running.
docker ps -a
```

```bash
docker pull hello-world
docker pull httpd:alpine
docker image ls
docker images
docker run -d -p 80:80 httpd:alpine
# check your instance's public ip
docker 
docker exec -it ce0b6ee1c658 sh
cd htdocs/
cat index.html
vi index.html
exit
# change content 
# observe the changes
curl localhost
# go to your instance's public ip
docker pull httpd
docker images
docker image ls
docker run -d -p 8080:80 httpd
docker exec -it 081d5350025f sh
cd htdocs/
cat index.html
vi index.html
# change content 
# observe the changes
curl localhost:8080
# go to your instance's public ip:8080
```
# Container Naming 
```bash
docker run --name turan -i -t ubuntu /bin/bash
hostname
# show running process
ps -ef
# explain running process
uname -a
# Linux 4459d1617f9c 5.15.0-1011-aws #14-Ubuntu SMP Wed Jun 1 20:54:22 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
# observe that containers share the same kernel with hosting docker server
cat /etc/os-release
exit
docker ps -a
docker start turan
docker ps
docker ps -a
docker start 342f5a96f3cd
docker stop 342f5a96f3cd
docker rm 342f5a96f3cd
docker inspect turan
docker stop turan 
docker rm turan
