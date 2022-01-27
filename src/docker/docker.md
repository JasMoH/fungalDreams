# some notes on what docker is for the uninitiated

New users to docker often find it hard to know where to start. Many tutorials dive right in to deploying 
containers without really saying what a container *is*. This attempts to collect some observations that
I've found useful for explaining docker to newbies.

## Docker is confusing
The reason why docker is confusing to learn about is because it isn't really *one* thing, it's
a collection of many tools that solve many problems. Because there's no single problem it solves, there's also
no single way it is used, hence the differences between docker run, docker-compose, and more advanced frameworks like
kuberenetes.

Docker can be used for some wildly different problems:
* controlling development and compilation environments
* coordinating server side services such as load balances and databases

These problems have very different needs, and use very different sets of features in different ways.

## Docker is chroot ... sorta
I find it easiest to start to think through what docker is by building up from something simpler.
The simplest thing that you could call "a container" is [chroot](https://en.wikipedia.org/wiki/Chroot) with 
a bunch of extra convenience tools. chroot allows you to have a separate file system in a box.
Notably, anything that runs in the chroot container still runs natively, and has access to anyting that isn't
hidden by the file system, such as network, physical hardware, kernel modules, etc. You can look at something like [pi-gen](https://github.com/RPi-Distro/pi-gen) as an example of something
that uses chroot to build up an entire operating system. 

The scripting in pigen is rather single purpose, so docker provides a bunch of convenience features
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

## Docker is more than chroot
chroot only provides isolation on the file system, docker also provides isolation on:
* mnt namespace provides a root filesystem
* pid namespace so the process only sees itself and its children
* network namespace which allows the container to have its dedicated network stack
* user namespace (quite new) which allows a non root user on a host to be mapped with the root user within the container
* uts provides dedicated hostname
* ipc provides dedicated shared memory

## There are many ways to use containers
There are a ton of tools for configuring and running containers: `docker run`, `docker-compose`, k8s, and others.
They all provide different ways to select your containers, and configure them to run in a bunch of different ways.
You can run containers interactively for debugging or developing other software, or cluster them together and spool them up based
on dynamic loading in a larger system 

These different user interfaces to the same underlying container system evolved somewhat independently, and feel
like they are almost unrelated tools. As best as I can tell, that's because they are. 

## Most of these differences are related to side-effects
If the containers perfectly isolated their contents from the outside world these programs would run
on the exact same files and environmental variables as inputs, isolated from the outside network every time.
Any changes they made to those files or variables are discarded, and nothing they try to send gets to the network.
This is useless! The whole point of computing is to do something with the real world other than burn CPU.
When you run a container you usually expose network ports, mount files, set environmental variables, and harvest the output.
In other words, controlling the side effects available for your container to change. These different tools have 
wildly different styles for specifying and managing all these options.

This also means a single container can have different uses and different behavior depending on how it is invoked.
This isn't any different from any other program that has run-time decision making based on reading some environmental input.

