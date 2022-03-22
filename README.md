# Docker
# Introduction to Docker

01

docker pull hello-world
Command to pull from Docker Hub.
Docker hub is the place where Docker store images.
You can check using docker image ls

02

To launch container you can simply use the run command.
docker run hello-world.

03

docker rmi -f $(docker images -q) 
docker rm -f $(docker ps -a -q)
docker run -d -p 5000:80 --name overlord --restart=always nginx

-rm remove - i image -f force

docker run :

-d --detach Run in the background
--name string to assign a name
-p, --publish list Publish a container's port to the host

-p port mapping <host port>:<container port>

--restart to specify a container's restart policy.
Restart policy controls whether the Docker daemon restarts a container after exit.
Docker supports the following restart policies :
-no
-on-failure[:max-retries]
-unless-stopped
-always

04
https://linuxconfig.org/how-to-retrieve-docker-container-s-internal-ip-address

docker inspect -f '{{.NetworkSettings.IPAddress}}' overlord

docker inspect --help:
-f, --format string Format the outuput using the given Go template.

05

Alpine Linux is a linux distribution build around music lbc and BusyBox.

docker run -it --rm alpine /bin/sh

-i, --interactive keep Standard Input open even if not attached.
-t, --tty create a pseduo TTY, (has the function of a physical terminal without actually being one, manpage pty)
> More reading about tty https://unix.stackexchange.com/questions/21147/what-are-pseudo-terminals-pty-tty

-- rm to remove automatically when it exits

Alpine doesnt contain bash, instead use /bin/ash /bin/sh, ash or only sh

06

install on a debian container everything you need to compile C source code and push it onto a git repo.

https://help.ubuntu.com/community/InstallingCompilers

To start a debian container
docker run -ti --rm debian

install git on it with apt-get install git
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install build-essential

build-essential is what is called a meta-package. It in itself does not install anything. Instead, it is a link to several other packages that will be installed as dependencies.
https://pimylifeup.com/ubuntu-build-essential/

Could aswell install separately needed tools
add -y --assume-yes to automatically answer yes to all prompts (cf man apt-get)
You could consider adding vim to the program to test out if it is working, kept it out as the question only require the commands.

07
Docker volumes are directories and files that exist on the host file system outside of the Docker container.
https://docs.docker.com/engine/reference/commandline/volume_create/
docker volume create --name hatchery

08
can check it with docker volume ls

09

docker run -d --name spawning-pool --restart=on-failure:15 --platform=linux/amd64 -e MYSQL_ROOT_PASSWORD=Kerrigan -e MYSQL_DATABASE=zerglings -v hatchery:/var/lib/mysql mysql --default-authentication-plugin=mysql_native_password

-e, --env list Set environement variables

MYSQL_ROOT_PASSWORD

this variable is mandatory specificies the password that will be set for the MySQL root superuser account.

MYSQL_DATABASE

this variable allows you to specifiy the name of database.

finally set default authentication as mysql_native_password
note that for mac apple m1 chipset you need to run the extra command --platform=linux/amd64 
https://stackoverflow.com/questions/65612411/forcing-docker-to-use-linux-amd64-platform-by-default-on-macos

10

docker inspect -f '{{.Config.Env}}' spawning-pool

11

docker run -d --name lair -p 8080:80 --link spawning-pool:mysql wordpress

--link is use to create the link
Links allow containers to discover each other and securely transfer information about one container to another container.
Ahen you set up a link, you create a conduit between a source container and a recipient container.
The recipient can then access select data about the source.
Check http://localhost:8080 it should display a wordpress installer
You can also setup the database directly using WORDPRESS_DB_HOST/USER ...
12

https://omarghader.github.io/docker-tutorial-phpmyadmin-and-mysql-server/
docker run --name roach-warden -d --link spawning-pool:db -p 8081:80 phpmyadmin/phpmyadmin

Phpmyadmin must point to MySQL Server in order to work.
To run the container use docker run --name myadmin -d --link mysql:db -p 
8080:80 phpmyadmin/phpmyadmin
(the previous command in our case)
On http://localhost:8081 you should have a phpMyAdmin prompt

13

docker logs -f spawning-pool
-f, --follow option to display live log output

14

docker ps

15

docker restart overlord

16

docker run -dit --name Abathur -v ~/:/root -p 3000:3000 python:2-slim
docker exec Abathur pip install Flask
echo 'from flask import Flask\napp = Flask(__name__)\n@app.route("/")\ndef hello_world():\n\treturn "<h1>Hello, World!</h1>"' > ~/app.py
docker exec -e FLASK_APP=/root/app.py Abathur flask run --host=0.0.0.0 --port 3000

with echo you put the code of into app.py
can double check on http://0.0.0.0:3000/

17

docker swarm init --advertise-addr machine IP (mac 127.0.0.1)

https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/

19

20

21

To change the default usermane and password of guest/guest you use RABBITMQ_DEFAULT_USER and RABBITMQ_DEFAULT_PASS
-d --detach exit immediately instead of waiting for the service to converge.

22

docker service ls

23

With an orbital-command running on your host/swarm, set 
0C_USERNAME : the username to access the orbital commadn
0C_PASSWD : the password to acces

24 

https://docs.docker.com/engine/reference/commandline/service_ps/
docker service create -d --network overming --name engineering-bay --replicas 2 -e 0C_USERNAME=johnny -e 0C_PASSWD=123123 42school/engineering-bay

25

docker service create -d --network overming --name marines --replicas 2 -e 0C_USERNAME=johnny -e 0C_PASSWD=123123 42school/marine-squad

same idea that with ex 23 and the orbital command just had it to it

26

docker service ps marines

ps list the tasks of the services

27

docker service scale -d marines=20

scale one or multiple replicated services.
-d --detach

Can check the logs with 
docker service logs -f $(docker service ps marines -f "name=marines.11" -q)

28

docker service rm $(docker service ls -q)
rm for remove

29

docker rm -f $(docker ps -a -q)

-f --force (uses SIGKILL)

30

docker rmi $(docker images -a -q)
rmi remove images


#DOCKERFILES

Exercise 00
docker build -t new_docker_image_name PATH_to_Dockerfile

FROM alpine:latest

RUN apk update
	&& apk add vim

Tell the docker to pull the latest Alpine docker image, update and install vim
&& to redo the previous command and keep it cleaner
#to run it > docker build -t clem/ex00 . (in the dockerfile folder, name/fpath)
#to build it > docker run --rm -ti ex00

Exercise 01

Voice use port 9987 udp
file transfer 30033 tcp
Query server 10011

once container is created need to make sure those are open
docker build --platform linux/x86_64 -t clem/ex01 . to build


Exercise 02

Docker file to containerize Rails app.
Dockerfile will be generic and called in another Dockerfile
That would be achieve through the given dockerfile command in the exercise
To make it easier to test out dl https://github.com/mhartl/sample_app_6th_ed

build it with docker build -t ft-rails:on-build ft-rails
docker build -t clem/ex02 ./   
test with docker run --rm -p 3000:3000 clem/ex02
should launch the app

Exercise 03

You have to make a Dockerfile that get us an instance of gitlab
the subject provide the girlab repo
use of debian as recommended on the repo

docker build -t clem/ex03 . to build
docker run -it --rm -p 8080:80 -p 8022:22 -p 8443:443 --memory="8g" --cpus"3" --privileged -e GITLAB_ROOT_PASSWORD="123123" clem/ginlab bash
