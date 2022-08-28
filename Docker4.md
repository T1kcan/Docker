# Docker Networking
systemctl status docker
docker network ls
docker container run -dit --name turan1st centos bash
docker container run -dit --name turan2nd centos bash
docker ps
docker network inspectalpine ashbu bridge | less
docker container inspect turan2nd | grep IPAddress
docker container attach turan1st
ifconfig
ip a
ping -c 4 google.com
ping -c 4 172.17.0.4
docker network create --driver bridge turannet
docker network ls
docker network inspect turannet
docker container run -dit --network turannet --name turan3rd centos bash
docker container run -dit --network turannet --name turan4th centos bash
docker container run -dit --name turan5th centos bash
docker container run -dit --name turan6th centos bash
docker attach turan3rd
ping -c3 turan4th
ping -c3 turan5th
docker network connect turannet turan5th
docker attach turan4th
ping -c3 turan5th
ping -c3 turan6th

# Docker Compose
# Installing Docker Compose
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" \
-o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

# create a YML file named as docker-compose.yml
vi docker-compose.yml
```
```yml
version: "3"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```
```bash
# make sure that Dockerfile and web_app.py present in current directory
docker-compose up
docker-compose up -d
docker-compose up --build -d

# create another directory and make another docker-compose example

```yml
services:
  db:
    # We use a mariadb image which supports both amd64 & arm64 architecture
    image: mariadb:10.6.4-focal
    # If you really want to use MySQL, uncomment the following line
    #image: mysql:8.0.27
    ...
  wordpress:
    image: wordpress:latest
    ports:
      - 80:80
    restart: always
```
docker compose up -d
Creating network "wordpress-mysql_default" with the default driver
Creating volume "wordpress-mysql_db_data" with default driver
...
Creating wordpress-mysql_db_1        ... done
Creating wordpress-mysql_wordpress_1 ... done
Expected Result:
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
5fbb4181a069        wordpress:latest    "docker-entrypoint.s…"   35 seconds ago      Up 34 seconds       0.0.0.0:80->80/tcp    wordpress-mysql_wordpress_1
e0884a8d444d        mysql:8.0.19        "docker-entrypoint.s…"   35 seconds ago      Up 34 seconds       3306/tcp, 33060/tcp   wordpress-mysql_db_1
# Navigate to http://localhost:80 in your web browser to access WordPress.
docker-compose down

```yml
version: "3.8"
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```