# Docker
Introduction to Docker

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


