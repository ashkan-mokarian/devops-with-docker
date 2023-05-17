# Intro to Docker
Notes, homeworks, and projects for [devops-with-docker](https://devopswithdocker.com/).

# Next Step

# TOC

* [Section: Basics](#docker-basics) Docker image, container, listing, removing, running, tags, flags, attaching, executing command in a container
* [Section: Dockerfile](#dockerfile) Dockerfile, basic example, ENTRYPOINT, CMD
* [Section: Interaction](#interacting-with-containers-via-volume-and-ports) mounted volume; port; securing open ports;
* [Section: General Considerations especially for building on M1](#general-consideration): Solution to qemu-x86_64 error;

# Examples

* [Simple example](./00-basic/Dockerfile)
* [ENTRYPOINT and CMD](./00-basic/youtube-dl.Dockerfile)
* [Interaction](./01-port-with-ruby/) - explanation [here](https://devopswithdocker.com/part-1/section-6)
* [Ex01: frontend](./ex01-frontend/)
* [Ex02: backend](./ex02-backend/)

# Notes

## Docker basics

_image_: template or blueprint; immutable; `docker image ls`; `docker image pull ...` pulls from hub to local; `docker image rm` or `docker rmi` for short removes;

_container_: instance of an image; isolated environment on a host machine. container can talk to other containers or host machine via TCP/UDP; `docker container ls`; add `-a` to not only see the running ones, but also the exited ones; or do `docker ps`; `docker container run ...` will pull if not avilable locally and run after downloaded; running with `-d` flag detaches and runs in background; detached ones cannot be removed and need to be stopped first by `docker container stop name` or by forcing `... rm --force name`; 

_Docker Engine_: consists of 3 parts: CLI, REST API, Docker Daemon;

**removing**: First remove all container, if you wanna remove image with `docker container rm id1 id2 id3`. you can use even 2 starting characters, don't worry its safe if multiple; `docker container prune`, `docker image prune`, `docker system prune` to remove all or dangling ones; 

`-d`: detached container run; `-t`: run a tty (a cli to talk to container); `-i`: interactive; `-it` put i and t together. `--name` give it a name; 

```bash
docker run -d -it --name looper ubuntu sh -c 'while true; do date; sleep 1; done'
```

`docker pause name`: pausing without stoping or exiting; and `docker unpause` to unpause duuuh; 

`docker attach {name}`: to attach but if you do <ctrl-c> you abort, because the stdin is also attached. otherwise if you do `docker attach --no-stdin` it won't listen to stdin; 

`docker start {name}` also exists; `docker logs -f {name}` to follow its stdout;

`docker exec -it looper bash` will execute a command on the container, here a bash with a tty in i mode on looper.

`docker kill {name}` kills a running container;

`--rm`: makes sure that container is removed after exiting, leaving no dead bodies behind. Also no one can resurrect them by `docker start` again.

When you 'containerize' an application, you work towards creating an image for it; `docker search` to search docker hub; To search other docker registry hosts you can do `docker search quay.io/hello-world`;

**tag**: different versions of images are marked with tags; `docker pull ubuntu:18.04` use :<tag>; tagging can also be used to rename images locally `docker tag ubuntu:18.04 fav-distro:bionic`; 

So images have the name format: `_registry/organization/image:tag_` but it can also be refered to as short as `image`;

## Dockerfile
<!-- <a name="#section-dockerfile"></a> -->
writing the reciepe to create the image from; Check [this](./00-basic/Dockerfile) for a simple one; Build it with `docker build . -t <image-name>`;

Copy from host machine to container using `docker cp source_file_on_host docker_name:path`; `docker diff cont_id` gives you a list of changes made to it; `docker commit old_name new_name` will create a new image with the changes being made while running. but never use this. not maintainable, just add what you need to Dockerfile;

### `CMD` vs. `ENTRYPOINT`
In a Dockerfile, command is by default set as '/bin/sh' and if you run a container, and put pwd as argument, it will pass thia argument to the default command.

If `ENTRYPOIN ["usr/local/bin/youtube-dl"]` is set instead of CMD, the argument to docker run will be passed as argument to the entrypoint and not replaced by it. therefore now you can e.g. provide a link to the youtube page to download.

If both CMD and ENTRYPOINT is used together, the value of CMD is used as default argument to ENTRYPOINT. 

Consider the following Dockerfile:
```docker
ENV LC_ALL=C.UTF-8  # rather than EXPORT LC_ALL...

ENTRYPOINT ["/usr/local/bin/youtube-dl"]

CMD ["https://imgur.com/gallery/xwJgflf"]  # default argument for entrypoint
```

and can be overriden by `docker run youtube-dl <url>` (apparently CMD is used as default and is overriden by providing additional context to run command).

## Interacting with containers via **Volume** and **ports**

### Interaction via filesystem - **Volume**
By using the `-v` flag, we can mount a folder on host so that files are persistent even after the container exits.

`docker run -v "$(pwd):/mydir" youtube-dl https://imgur.com/JY5tHqr`: This will mount pwd on host as a volume that is connected to /mydir in container and every changes are reflected in both directions. This can also be used for a single file, such as `-v "$(pwd)/material.md:/mydir/material.md"`. Now every change we make to the file will be reflected in both directions.

### Interaction via **Port**

The other way to interact from outside with a container is with port. Lets disect `http://127.0.0.1:3000`. It consists of three parts: 1. _Protocol_: here is http. 2. _IP_: 127.0.0.1 here or equivalently localhost. 3. _Port_: 3000 here. 

Programs can be assigned to listen to a specific available port. If a program is listening for traffic on port 3000, and a message is sent to that port, it will recieve it.

Opening a connection from outside to a docker container happens in two steps:
1. Exposing port: means telling docker that the container listens to a certain port. To expose add line to dockerfile `EXPOSE <port>`.
2. Publishing Port: means that Docker will map host ports to the container ports. Publishing is done as a flag to the run command with `-p <host-port>:<container-port>`. If host port is left out, Docker assigns a free port of the host.

For limiting the protocol to 'udp' only for example use: `EXPOSE <port>/udp` and `... run -p <>:<>/udp`.

### **Security** concerns
By default when running `-p 3456:3000`, we open the 3456 port to anyone, even from outside cuz its the same as `-p 0.0.0.0:3456:3000`. In order to prevent that and only allowing local host to connect to that port is by using `-p 127.0.0.1:3456:3000`. Usually however, this is not risky, but should be considered in particular for specific applications.

## General Consideration

### Building on ARM architectures, M1

When building from an image like ubuntu, I don't know why but the emulated framework looks for x86_64 pathes where the image was built on a arm arch for example M1 mac. For example, when building the [backend exercise](./ex02-backend/), which builds from the original ubuntu image, and then downloading the Linux tar file for installing golang, the container will look for x86_64 library paths and lead to the following error "qemu-x86_64: Could not open '/lib64/ld-linux-x86-64.so.2': No such file or directory". Workarounds to this problem is shown for example [here](https://stackoverflow.com/questions/71040681/qemu-x86-64-could-not-open-lib64-ld-linux-x86-64-so-2-no-such-file-or-direc), and what I used here was just building the image with the platform option `docker build --platform linux/amd64`. 'amd64' is the same as x86_64 and different from arm64.