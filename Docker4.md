# Docker Networking
systemctl status docker
docker network ls
docker container run -dit --name turan1st alpine ash
docker container run -dit --name turan2nd alpine ash
docker ps
docker network inspect bridge | less
docker container inspect turan2nd | grep IPAddress
docker container attach turan1st
ifconfig
ip -a
ping -c 4 google.com
ping -c 4 172.17.0.3
docker network create --driver bridge clarusnet
docker network ls
docker network inspect clarusnet
docker container run -dit --network turanet --name clarus1st alpine ash
docker container run -dit --network turannet --name clarus2nd alpine ash
docker container run -dit --name turan3rd alpine ash
docker container run -dit --name turan4th alpine ash
docker network connect turannet turan4th
