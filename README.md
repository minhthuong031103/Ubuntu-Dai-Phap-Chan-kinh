- [Check version ubuntu](#check-version-ubuntu)

- [Show all active port](#show-all-active-port)
- [Add user Ubuntu](#add-user-ubuntu)
- [install node nvm](#install-node-nvm)
- [Install rabbitmq](#install-rabbitmq)
- [Nginx config](#nginx-config)
- [Install docker, docker desktop and docker compose:](#install-docker-docker-desktop-and-docker-compose)
  - [Executing the Docker Command Without Sudo](#executing-the-docker-command-without-sudo)
  - [Go into docker container shell:](#go-into-docker-container-shell)
  - [Disable/ enable docker on startup:](#disable-enable-docker-on-startup)
- [Install java and manage version](#install-java-and-manage-version)
- [Install Mongodb](#install-mongodb)
  - [Install mongodb compass](#install-mongodb-compass)
  - [Create database and user for mongo database](#create-database-and-user-for-mongo-database)
  - [Some command in mongo shell:](#some-command-in-mongo-shell)
- [Install Mysql](#install-mysql)
  - [Setup mysql authen](#setup-mysql-authen)
  - [Install mysql workbench](#install-mysql-workbench)
- [Install redis](#install-redis)
  - [Test redis server](#test-redis-server)
  - [Install redis insight](#install-redis-insight)

# Check version ubuntu

- lsb_release -a

# Show all active port

- ss -tuln

# Add user Ubuntu

- sudo adduser oscar
- sudo usermod -aG sudo <username>: add user to sudo
- sudo usermod -aG <group_name> <username>: add user to user group
- sudo chown <username>:<group_name> /path/to/<folder_name>
- <a> https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-20-04 </a>

# install node nvm

- cd ~
- curl -sL https://deb.nodesource.com/setup_16.x -o /tmp/nodesource_setup.sh change 16 to any node version
- sudo bash /tmp/nodesource_setup.sh wait for 1-2 minutes
- npm i -g yarn
- apt update && apt install sudo curl && curl -sL https://raw.githubusercontent.com/Unitech/pm2/master/packager/setup.deb.sh | sudo -E bash -

# Install rabbitmq

- sudo apt install rabbitmq-server
- rabbitmqctl add_user oscar abcxyz
- rabbitmqctl set_user_tags oscar administrator
- rabbitmqctl set*permissions -p / admin ".*" ".\_" ".\*"
- rabbitmq-plugins enable rabbitmq_management
- sudo systemctl enable/disable/start/restart/stop/status rabbitmq-server
- rabbitmqctl change_password oscar password

# Nginx config

## Nginx config for serverport
- write nginx conf in sites-available and ln
- touch oscar-be.conf
- nano oscar-be.conf

```ts
server {
  listen         80;
  listen         [::]:80;
  server_name    yourserver.com;

  client_max_body_size 128M;

  location / {
    proxy_set_header X-Real-IP $remote_addr ;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for ;
    proxy_set_header X-Forwarded-Proto https;
    proxy_pass http://localhost:3013;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
  }
  # Do this if you want alias to a folder file in server
  location /uploads/ {
    add_header Access-Control-Allow-Origin *;
    alias /home/friendify/backend-chat-gpt/uploads/;
  }
}






```

## Nginx config for html static 
``` ts
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		try_files $uri /index.html;
	}

	# Other configurations...
}


```


- systemctl -t nginx
- systemctl restart nginx
- ln -s /etc/nginx/sites-available/oscar-be.conf /etc/nginx/sites-enabled
- certbot --nginx -d oscar-api.friendify.ai => will automatically add ssl 443 to conf

# Install docker, docker desktop and docker compose:

<a> https://www.youtube.com/watch?v=Vplj9b0L_1Y </a> <a> https://docs.docker.com/desktop/install/ubuntu/ </a> <a> https://docs.docker.com/desktop/install/ubuntu/ </a>

- sudo apt-get update

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

- sudo apt install ./docker-desktop-4.26.0-amd64.deb sudo apt-get update

## Executing the Docker Command Without Sudo

<a>https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md</a> newgrp docker

## Build Docker images and up to Docker hub
- docker build -t tsme-frontend .
- docker tag tsme-frontend minhthuong031103/tsme:latest
- docker push minhthuong031103/tsme:latest
- docker pull minhthuong031103/tsme:latest

## Go into docker container shell:

- docker exec -it container_id bash
- psql -h localhost -U minhthuong

## Disable/ enable docker on startup:

- systemctl list-unit-files | grep -i docker
- sudo systemctl disable docker.service
- sudo systemctl disable docker.socket

# Install java and manage version

- sudo apt install openjdk-17-jdk -y
- sudo apt install openjdk-11-jdk -y
- update-java-alternatives -l : List versions
- sudo update-alternatives --config java  
  ![image](https://github.com/minhthuong031103/minhthuong-blog/assets/101078033/67e45eb6-6bc0-4bfa-8d10-e66375e22cf0)

# Install Mongodb

> You can down amd64.deb of mongo server community and mongo shell or install both on mongo docs

- sudo dpkg -i mongo-org-server_6.0.5_amd64.deb
- sudo systemctl enable/disable/start/restart/stop/status mongod

![image](https://github.com/minhthuong031103/minhthuong-blog/assets/101078033/fd330529-6a9f-4f9d-aec4-ce2c4fc9e6f7)

- sudo dpkg -i mongo-mongosh_1.8.2_amd64.deb
- mongosh
- show dbs;

## Install mongodb compass

- <a> https://www.mongodb.com/try/download/shell </a>

## Create database and user for mongo database

- use oscars;
- db.createUser({ user : "oscars", pwd: "abcxyz", roles: [ { role: "readWrite" , db: "oscars"} ] }) nano /etc/mongod.conf: security: authorization: enabled
  - db.createUser({ user: "admin", pwd: "Minhno123@", roles: ["root"], });
- db.getUsers()
- db.updateUser("admin", { pwd: "new_password", }); => everytime use oscars, call db.auth("oscars")

## Some command in mongo shell:

- use db_name
- db.createCollection("name")
- show collections
- db.collectionName.find({})
- db.collectionName.updateOne( { email: "phngtest123@gmail.com" }, { $set: { points: 0 }, })

# Install Mysql

- sudo apt install mysql-server
- sudo systemctl status/start/restart/stop/enable mysql-server
- sudo mysq
- mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';

## Setup mysql authen

- sudo mysql_secure_installation
- set up validate password component => no
- change password => yes
- remove anonymous user => no
- disallow root remote => no
- remove test database => no
- reload privilege => yes
- sudo mysql -u root -p => DONE

## Install mysql workbench

- sudo snap install mysql-workbench-community

# Install redis

- sudo snap install redis
- sudo snap enable/start/stop/restart redis
- sudo snap services
- sudo nano /etc/redis/redis.conf change requirepass yourpassword supervised systemd  
  bind 127.0.0.1 ::1 **The default username of redis-server is "default"**

## Test redis server

- ss -an | grep 6739
- redis.cli => redis.cli => enter redis server
- ping
- set test "my_value"
- get test

## Install redis insight

- sudo dpkg -i RedisInsight-linux-amd64.deb
