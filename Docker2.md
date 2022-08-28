# make sure that port 22, 80 and 8080 is allowed in security group of instances
docker search ubuntu
# Running containers in detach (deamon) mode
docker run -it -d ubuntu
# while holding ctrl button press p then q
# container will continue to run in background
docker ps
docker help run | more
# -d detach (background)
# --restart unless-stopped
# going inside running container: attach
docker attach 66211704cabb
docker attach be4c
# Images vs Containers
docker run --name new -it ubuntu /bin/bash
apt-get update
apt-get upgrade -y
apt-get install vim -y
vim
exit
docker run --name new1 -it ubuntu /bin/bash
vim
# containers are stateless, the ubuntu container image which we run and create container form has not been changed. Thence everytime you run the image containers will be the same.
exit
docker ps -a
# observe that each container has different Id
docker image ls 
# Restart the stopped state container by its `ID`
docker start new
docker attach new
vim
# new is a container created from ubuntu image and we have made some changes. Understand difference between images and containers
docker start 01f3681b1c02
docker start 01
docker ps
docker attach 01
ls
whoami
cat /etc/os-release
mkdir test
cd test
echo "just for testing" > sample.txt
exit
docker stop 01
# The main process inside the container will receive SIGTERM, and after a grace period, SIGKILL. The first signal can be changed with the STOPSIGNAL instruction in the container’s Dockerfile, or the --stop-signal option to docker run.
docker ps
docker ps -a
# copying files between containers and host vm
docker help cp
docker cp 01f3681b1c02:/test .
docker cp serene_buck:/usr/local/apache2/htdocs/index.html .
docker cp aa.txt serene_buck:/usr/local/apache2/htdocs/
docker cp index.html 01f3681b1c02:/test
docker start 01
docker attach 01
ls test
exit
# details about docker container
docker inspect 01
docker inspect serene_buck | more

# docker volumes
docker help volume

# when we want to create a folder in container named as "/var/lib/mysql/data" there are 3 ways to do it:

# 1 You decide where on the host file system the reference is made
# docker run -v /home/mount/data:/var/lib/mysql/data
docker run -it -v /home/mount/data:/var/lib/mysql/data alpine sh
# folder is created under host vm /home/mount/data
# go to /var/lib/mysql/data
exit
# go to /home/mount/data on hosting vm
cd /home/mount/data
sudo touch a.txt
docker start b8def38cf8d3
docker attach b8def38cf8d3
cd /var/lib/mysql/data
ls

# 2 For each container a folder is generated that gets mounted
# docker run -v /var/lib/mysql/data
docker run -it -v /var/lib/mysql/data alpine sh
cd /var/lib/mysql/data
ls
exit
# folder is created under host vm /var/lib/docker/volumes/random-hash/_data
sudo ls /var/lib/docker/volumes/

# 3 You can reference the volume by name; should be used in production
# docker run -v name:/var/lib/mysql/data
docker run -it -v test1:/var/lib/mysql/data alpine sh
cd /var/lib/mysql/data
ls
exit
# folder is created under host vm /var/lib/docker/volumes/random-hash/_data
sudo ls /var/lib/docker/volumes/


docker volume create vlm_one
docker volume inspect vlm_one
sudo ls -al  /var/lib/docker/volumes/cw-vol/_data
docker volume ls
docker volume rm
docker volume prune
# [ec2-user@ip-172-31-94-153 ~]$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
# Attaching volumes into containers:
--volume <volume_name>:<path>:<list of options>

docker run -it --name turan -v vlm_one:/host_volume alpine ash
# inside the container type ls:
ls 
cd host_volume
echo "This file has been created inside Alpine Linux under host_volume" >file1.txt
exit
docker rm turan
docker ps -a
sudo ls -al  /var/lib/docker/volumes/vlm_one/_data
sudo ls -al  /var/lib/docker/volumes/vlm_one/_data && sudo cat /var/lib/docker/volumes/vlm_one/_data/file1.txt
docker run -it --name turan2 -v vlm_one:/host_volume2 alpine ash
ls
# observe host_volume2 is present
ls host_volume2
cd host_volume2
cat file1.txt
echo "This file has been created inside Alpine Linux under host_volume2" >file2.txt
ls
# observe file1.txt and file2.txt are listed
exit
docker run -it --name turan3 -v vlm_one:/host_volume3:ro ubuntu bash
ls
cd host_volume3
cat fi*
echo "This file has been created inside Alpine Linux under host_volume3" >file3.txt
# bash: file3.txt: Read-only file system
exit
sudo ls -al  /var/lib/docker/volumes/vlm_one/_data
docker ps -a
docker rm b4 af 8e

# docker volume behaviours

|No    | Situation   | Behaviour |
| ---- | ----------- | ------------ |
| 1    | If there is no target directory. | The target directory is created and files inside volume are copied to this directory. |
| 2    | If there is target directory, but it is empty. | The files in volume are copied to target directory.  |
| 3    | If there is target directory and it is not empty, but volume is empty. | The files in the target directory are copied to volumes. |
| 4    | If the volume is not empty. | There will be just the files inside the volume regardless of the target directory is full or empty. |

# binding volumes
docker run -d --name nginx-default -p 80:80  nginx
# observe http://<public-ip>:80
```bash
docker exec -it nginx-default bash
cd /usr/share/nginx/html
/usr/share/nginx/html# ls
# 50x.html  index.html
cat index.html
exit
# Create a folder named  webpage, and an index.html file.
```bash
mkdir web && cd web
echo "<h1>Welcome Docker Web Page</h1>" > index.html
docker run d- --name nginx-new -p 8080:80 -v /home/ec2-user/web:/usr/share/nginx/html nginx
# observe http://<public-ip>:8080
docker exec -it nginx-new bash
cd usr/share/nginx/html
ls 
# index.html
cat index.html 
exit
echo "<h2>This is added for docker volume lesson</h2>" >> index.html
# observe the change


# cleaning  
docker stop $(docker ps)
docker rm -f $(docker ps -aq)
docker container prune
docker volume prune
docker volume prune -f
docker volume ls
docker container ls

