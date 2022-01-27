# some notes on what docker is for the uninitiated

## Docker is confusing
The reason why docker is confusing to learn about is because it isn't really *one* thing, it's
a collection of many tools that solve many problems. Because there's no single problem it solves, there's also
no single way it is used, hence the differences between docker run, docker-compose, and more advanced frameworks like
kuberenetes.

Docker can be used for some wildly different problems:
* controlling development and compilation environments
* coordinating server side services such as load balances and databases

These problems have very different needs, and use very different sets of features in different ways.

## Docker as chroot
I find it easiest to start to think through what docker is by building up from something simpler.
The simplest thing that you could call "a container" is [chroot](https://en.wikipedia.org/wiki/Chroot) with 
a bunch of extra convenience tools. chroot allows you to have a separate file system in a box.
Notably, anything that runs in the chroot container still runs natively, and has access to anyting that isn't
hidden by the file system, such as network, physical hardware, kernel modules, etc. You can look at something like [pi-gen](https://github.com/RPi-Distro/pi-gen) as an example of something
that uses chroot to build up an entire operating system. 

The scripting in pigen is rather single purpose, so docker provides a bunch of convinienc features
to manage building and running containers. this [stack exchange](https://devops.stackexchange.com/questions/2826/difference-between-chroot-and-docker)
article lists the nice bits:

* A standardized script file format including semantics relating to the task a hand
* Images (including anonymous in-between images), caching, naming, downloading etc. including powerful management (docker image prune ...)
* Containers (including their own temporary file systems, naming, being able to docker exec into them etc.)
* Process management (docker container ...)
* Networking with just a simple option, including intra-docker-container-networking etc.
* Volumes (including special managed volumes)
* docker-compose or swarm as low-profile upgrades to much more.
* The large zoo of other solutions based on dockerized containers (OpenShift etc.).

## Docker as more than chroot
chroot only provides isolation on the file system, docker also provides isolation on:
* mnt namespace provides a root filesystem
* pid namespace so the process only sees itself and its children
* network namespace which allows the container to have its dedicated network stack
* user namespace (quite new) which allows a non root user on a host to be mapped with the root user within the container
* uts provides dedicated hostname
* ipc provides dedicated shared memory


