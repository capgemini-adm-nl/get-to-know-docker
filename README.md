## Authors 
@Hijmen Fokker

@timschadenberg

# Docker Workshop assignments 
In this workshop we have prepared four assignments for you and two bonus assignments. Each assignment contains several tasks which help you get to know Docker and it's various features. As you progress through the assignments more advanced concepts will be introduced and brought together. Feel free to ask questions!

## Preparation: Installing Docker
**Goal: Install Docker on your Windows host.**

The aim of these assignments are to get you familiar with the Docker commands and basic concepts. Currently the Docker Toolbox is the easiest way to get your machine set up, so you don't have to deal with different VirtualBox configurations and Virtual Machine (VM) images. To best way of getting to know Docker is using the Docker Command Line Interface (CLI). Nonetheless, most of what we are doing in this workshop will be possible within the GUI (Kitematic) as well.

So to get started with Docker first you need to install the Docker Toolbox which provides you with not only the Docker Client, but also [Docker Machine](https://docs.docker.com/machine/overview/) (which controls the Docker Engine) and [Docker Compose](https://docs.docker.com/compose/overview/) (which allows you to specify a multiple container setup in a single file) and a GUI to easily control your Docker images and containers.

You can grab Docker Toolbox at [github.com/docker/toolbox](https://github.com/docker/toolbox/releases/download/v1.13.1/DockerToolbox-1.13.1.exe).
While you are downloading the installer go ahead and create your free Docker Hub account. You will use this account in the first assignment where you create your first container. You can signup for an account at [hub.docker.com](https://hub.docker.com/)


## Assignment 1: Getting to know Docker
**Goal: Learn basic Docker commands and pull first image from the Hub.**

In the previous chapter you have created your Docker Hub account and installed the Docker Toolbox. With this you are now ready to get started with Docker.

Start the Kitematic (Docker GUI) and login with your Hub account. Under the hood, a Linux VM will now be created which has the Docker environment installed. Once you are logged in open up the Docker CLI (you can find it in the bottom left corner). The Docker CLI will interface with the Docker VM and enables you to execute Docker commands from within Windows or Mac OS. You will notice that the Docker CLI is just a Windows PowerShell terminal.

> Alternatively you can open a GIT bash shell in Windows and control Docker from there. 

### 1.1 Images
First we will download a ready-made image from the Docker Hub. Download an image of the latest Ubuntu release by entering the following command:
``` sh
docker pull ubuntu:latest
```

You can see that multiple *layers* are being downloaded.
``` sh
latest: Pulling from library/ubuntu

d38575f188e0: Downloading [================>                                  ] 21.59 MB/65.69 MB
b04ea90f261c: Download complete
40dc9cd44ffa: Download complete
a3ed95caeb02: Download complete
d38575f188e0: Pull complete
b04ea90f261c: Pull complete
40dc9cd44ffa: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:4bc45eecd925cb8806294d0e8bc5c1cfd1149c6dd8a5ef2165b6c02eabb8821f
Status: Downloaded newer image for ubuntu:latest
```

To get an overview of all the images on your Docker host, you can use the `docker images` command. Try it out and check if the Ubuntu image is listed!

``` sh
docker images
docker images –-all 
```

> Use the `--all` or `-a` flag to also show intermediate images. Intermediate images are layers of another image, identified by `<none>:<none>`.

Now enter the following command to list all running and stopped containers.
``` sh
docker ps
docker ps --all
```
Wait a minute! Why are there no containers in the list!? Of course we haven't started any containers yet! 

Now that you have an understanding how to get Docker images it is time to use the Ubuntu image and create a container with it. 

### 1.2 Creating containers

Enter the following command to start your Ubuntu container:
``` sh
docker run ubuntu echo ‘Hello World!’
```
The `ubuntu` command line option is the name of the image to run. As with most docker commands instead you could also use the ID.

> Docker will automatically attempt to download images that it can't find locally from the Docker Hub.

Next use the docker commands from paragraph 1.1 to list the containers. You should see something like this:
``` sh
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS                       PORTS               NAMES
81eb4a56c3ef        ubuntu                          "echo 'Hello World!'"    11 seconds ago      Exited (0) 10 seconds ago                        nauseous_goldberg
```

As you can see Docker has created a container with ID **81eb4a56c3ef** from IMAGE **ubuntu**. Because we didn't specify a name, Docker gave it a name itself: **nauseous_goldberg**. You might wonder why this container has the status **EXITED**. When you create a container and tell Docker to run a command inside it, the container will exit once this command ends. In this example we told Docker to echo the infamous 'Hello World!' text. Once this text was echoed the container exited.
Let's see this in another example.

Create a new container and use the following options to keep it alive:
``` sh
docker run --interactive --tty ubuntu /bin/bash
```

The `--interactive --tty` options tells Docker to run the image in interactive mode with a terminal. The `/bin/bash` part is the name of the executable inside that image that we want to start. In this case it will launch a standard Bash shell. 

The prompt will change to something similar to:
``` sh
root@8dc395dca5e7:/#
```

Close the bash shell by typing `exit` to return to the Docker CLI. Note that your container will also be stopped.

### 1.3 Controlling containers
In the previous task we saw that container processes sometimes exit after you run them. Use the `docker start` command to restart a stopped container. The `--attach`  flag will attach the PowerShell's standard output (STDOUT) and error streams (STDERR) to those of the container and also cause all signals to be forwarded to the container.

``` sh
docker start --attach --interactive <CONTAINER NAME OR ID>
```

Exit the bash shell if necessary and try out the same command again without the `--interactive option`. If you are stuck you can use Ctrl-C to stop the container process.
In situations where your container is already running and you want to open a new shell or process you should use two other commands.
Look up the `docker attach` and `docker exec` commands in the [Docker Commandline Reference](https://docs.docker.com/engine/reference/commandline/docker/) and use them to open a new bash shell inside your running ubuntu container. Consider which command line options are needed.

Docker `attach` let's you use only one instance of shell. On the other hand, `docker exec`  will let you run arbitrary commands inside an existing container. So if we want open new terminal with *new* instance of container's shell, we need to run `docker exec`. 
If your container is running a webserver for example, `docker attach` will probably connect you to the STDOUT of the webserver process. It won't necessarily give you a shell. 

When you have downloaded several images and have created some containers to fiddle around with, you can see that it can become messy very quickly. 
Use the `docker rm` command to cleanup some one of the containers you have just created. Afterwards check if it removed correctly using `docker ps`.
``` sh
$ docker rm nauseous_goldberg
nauseous_goldberg
$ docker ps --all
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS                        PORTS               NAMES
```

 
## Assignment 2: Create your own image
**Goal: Create and push new images to the Docker Hub.** 

The previous assignment taught you how to work with existing Docker images. In this assignment we are going to create our own image!

### 2.1 Adding a layer

1.	Start an Ubuntu container with a Bash shell.
2.	Create a random file in your container. Hint: use `touch` or a text editor like **vi**.
3.  Exit the container's Bash shell.

For example:
``` sh
root@ff387f4bb704:/# ls -ltr
total 64
drwxr-xr-x   2 root root 4096 Apr 10  2014 mnt
drwxr-xr-x   2 root root 4096 Apr 10  2014 home
drwxr-xr-x   2 root root 4096 Apr 10  2014 boot
drwxr-xr-x   2 root root 4096 Mar 23 10:05 srv
drwxr-xr-x   2 root root 4096 Mar 23 10:05 opt
drwxr-xr-x   2 root root 4096 Mar 23 10:05 media
drwxr-xr-x   2 root root 4096 Mar 23 10:05 lib64
drwxr-xr-x   7 root root 4096 Mar 23 10:05 run
drwxr-xr-x  12 root root 4096 Mar 23 10:05 lib
drwxr-xr-x   2 root root 4096 Mar 23 10:06 bin
drwxrwxrwt   2 root root 4096 Mar 23 10:06 tmp
drwxr-xr-x  12 root root 4096 Apr  5 00:18 var
drwxr-xr-x  11 root root 4096 Apr  5 00:18 usr
drwxr-xr-x   2 root root 4096 Apr  5 00:18 sbin
drwxr-xr-x  64 root root 4096 Apr  7 07:12 etc
drwx------   2 root root 4096 Apr  7 07:12 root
dr-xr-xr-x 129 root root    0 Apr  7 07:13 proc
drwxr-xr-x   5 root root  380 Apr  7 07:13 dev
dr-xr-xr-x  13 root root    0 Apr  7 07:13 sys
root@ff387f4bb704:/# cd home
root@ff387f4bb704:/home# ls
root@ff387f4bb704:/home# touch myfile.txt
root@ff387f4bb704:/home# ls
myfile.txt
root@ff387f4bb704:/home# exit
```

What we did is start a (new) container and made some changes in it. Maybe you are interested in working with a particular tool and want to add this to your Ubuntu environment, say  `apt-get install nano`. The packages and files that are added/changed are not baked into the container image, just into this container instance. 
To see what the differences are between the image and the container try out the `diff` command. 

``` sh
$ docker diff gigantic_babbage
C /home
A /home/myfile.txt
C /root
A /root/.bash_history
```

Restart the container in which you made the changes. Is the file still there?
Add some more files or packages to your container until you are satisfied with the result.

### 2.2 Commiting your image

Ok, so we have our container and the changes you made inside it. We want to use this container as a base image to start new containers. In order to create an image from a container we use `docker commit`. `commit` will create a new layer on top of the Ubuntu base image.
``` sh
docker commit <CONTAINER NAME OR ID> <IMAGE NAME:TAG>
``` 
Use the docker command to show all your images to check if your image is in the list. Notice the *VIRTUAL SIZE* of your image.

## Assignment 3: Build your own image
**Goal: Understand and learn to use Dockerfiles.**

### 3.1 Docker build

Using `docker commit` is a pretty simple way of extending an image but it’s a bit cumbersome and it’s not easy to share a development process for images amongst a team. Instead you can use a new command, `docker build`, to build new images from scratch.

This command uses a [Dockerfile](https://docs.docker.com/engine/reference/builder/) which contains a set of instructions that tell Docker how to build our image.
In the previous assignments we already saw what a Dockerfile looks like in the Docker Hub. Now we will look at it in more detail.

Grab the Dockerfile from this [link](https://github.com/capgemini-adm-nl/get-to-know-docker/blob/master/assignment-3/3.1/Dockerfile), and save it to a particular folder in your project workspace. Examine the Dockerfile and skim through the Dockerfile reference guide to lookup some of the instructions that are used.  

Once you have a rough understanding of what goes on inside, use the Docker CLI to build an image from the Dockerfile. 

``` sh 
docker build --tag=<NAME OF YOUR IMAGE> /path/to/Dockerfile
```

The first thing Docker does is upload the build context: basically the contents of the directory you’re building in. This is done because the Docker daemon does the actual build of the image and it needs the local context to do it.
Next you can see each instruction in the Dockerfile being executed step-by-step. You can see that each step creates a new container, runs the instruction inside that container and then commits that change - just like the `docker commit` workflow you saw earlier. When all the instructions have executed you’re left with the newly created image and all intermediate containers will get removed to clean things up.


### 3.2 Create your own Dockerfile

The next step is to create your own Dockerfile. Use Windows explorer to browse to your preferred project workspace and create a file named *Dockerfile* (without a file extension).
Use the example below with the [Dockerfile Reference Guide](https://docs.docker.com/engine/reference/builder/) and [Dockerfile Best practices](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/) to create and build your image.

``` Dockerfile
# This is a comment
FROM centos:centos6
MAINTAINER Joe the Developer <joe.dev@capgemini.com>

# Update the repository sources list and install some packages
RUN yum -y update; yum clean all
RUN yum -y install epel-release; yum clean all
RUN yum -y install nginx; yum clean all

# Create some files
RUN echo "daemon off;" >> /etc/nginx/nginx.conf
RUN echo "nginx on CentOS 6 inside Docker" > /usr/share/nginx/html/index.html

# Copy your project from the host to the image
COPY /www /usr/share/nginx/html

# Expose a port
EXPOSE 80

# By default, simply start nginx.
CMD [ "/usr/sbin/nginx" ]
```

Instructions are capitalized and followed by a statement. The first instruction `FROM` tells Docker what the source of our image is, in this case you’re basing our new image on an CentOS 6 image. The instruction uses the `MAINTAINER` instruction to specify who maintains the new image.

The example also specified two `RUN` instructions. A `RUN` instruction executes a command in a new layer on top of the current image and commit the results. 

Build your Dockerfile with the command from task 3.1 to really see the build process at work. 
You can run the example above by running the command `docker run -d -p 80:80 <IMAGE NAME>`. 


## Assignment 4: Port mapping
**Goal: Learn how to map ports of a container to your host machine.**

In this assignment we will learn how port mapping from containers to the Docker host can be managed. Each container has its own network and ip-address.
Open a browser and navigate to the latest Tomcat image on the Docker Hub: [https://hub.docker.com/_/tomcat/](https://hub.docker.com/_/tomcat/)
Click on a Dockerfile and take note that this image is based on a Java image (`FROM java:8-jre`). Also, take note of the line `EXPOSE 8080`. This means port 8080, which is used by Tomcat, will be exposed to the docker-host.
Download the newest Tomcat image from the Docker Hub. Take note that a lot of layers have to be downloaded for this image to be ready (base Operating System, Java, Tomcat).
Start-up a container in the background (`-d`) as a daemon from this Tomcat image. Use the `-p` argument to map a port to the docker-host port: `-p <host>:<container>`
``` sh
docker run -d -p 1000:8080 tomcat
```

Start-up another container from this same Tomcat image, mapped to a different port. Use an interactive (`-i`) terminal (`-t`) to show the Tomcat STDOUT and STDERR stream messages
``` sh
docker run -i -t -p 1001:8080 tomcat
```
List the running containers, and take not that the ports are mapped to your defined ports (1000 and 1001).
``` sh
docker ps
```

Get the ip-address of the docker-host machine in a different terminal
``` sh
docker-machine ip default
``` 
Start a web-browser, and navigate to the Tomcat webinterface of the different containers:

`http://<docker-machine-ip>:1000`

`http://<docker-machine-ip>:1001`

<img src="https://github.com/capgemini-adm-nl/get-to-know-docker/blob/master/img/tomcat.png" width="500" />


## Bonus Assignment: Use Docker Compose

Running commands like we did above can be quite cumbersome and prone to human error. Docker Compose can help us out as it allows us to specify a single file in which we can define our entire environment structure and run it with a single command (much like a [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/) works).

Let’s create a docker-compose.yml file:
``` YAML
wordpress:
  image: wordpress
  environment:
    - WORDPRESS_DB_PASSWORD=password
  ports:
    - "80:80"
  volumes:
    - ./:/var/www/html
  links:
    - wordpressdb:mysql

wordpressdb:
  image: mysql:5.7
  environment:
    - MYSQL_ROOT_PASSWORD=password
    - MYSQL_DATABASE=wordpress
```
You can see how we’ve just replicated the commands above into a yml file (using the [Compose File reference](https://docs.docker.com/compose/compose-file/)). Now all we need to do is run:

```sh
$ docker-compose up -d
``` 


## Bonus Assignment: Sharing images
**Goal: Learn how to push your images to the Docker Hub.**

Do you want to share your custom image with other developers or friends? Then you can upload it to the public Docker registry (Docker Hub) or your own private Docker Registry.

Unlike the previous search and pull commands, you must be logged in to push. If you're not already logged in, Docker will prompt you for your credentials.
Login to the Docker Hub:
``` sh
docker login --username=<DOCKER ACCOUNT NAME> --email=<EMAIL ADDRESS>
```
And push your image.

``` sh
$ docker push mynewimage:latest

2016/04/07 18:00:00 You cannot push a "root" repository. Please rename your repository in <user>/<repo> (ex: timschadenberg/mynewimage:latest)
```` 
This rather self-explanatory error occurs because we didn't specify a username when we created the image. To push to the Docker Hub, though, you must specify a username. 
So, let's add our username using the docker tag command:
``` sh
docker tag <IMAGE ID> <DOCKER ACCOUNT NAME/IMAGE NAME:TAG>
```

Once you've retagged it you can upload your newly created image!
``` sh
docker push <DOCKER ACCOUNT NAME/IMAGE NAME:TAG>
```

Finally try to `pull` an image from your colleague or friends from the Hub and create a container with it. Can you see the changes that he/she made?


---


``` sh 
 _______________________________________________________________
/                                                               \
|                                                               |
|                    Congratulations!                           |
|                                                               |
|    You have finished all the Docker workshop assignments.     |
|    Continue at https://github.com/docker/labs for more        |
|    tutorials.                                                 |
\                                                               /
 ---------------------------------------------------------------
                \
                 \
                  \
                          ##        .
                    ## ## ##       ==
                 ## ## ## ##      ===
             /""""""""""""""""___/ ===
        ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
             \______ o          __/
              \    \        __/
                \____\______/
```