---
title: "Virtual machine trouble?? Try Docker!"
date: 2017-05-13T22:17:04-07:00
draft: false
---

If you are a Oregon State CS 344 student, then you've been told to develop exclusively on the OS1 server. Unfortunately, this server is frequently nuked by fork bombs. If you are unable to run a full CentOS virtual machine, then here is a step by step guide to getting a CentOS docker container running on your computer. This way, you can continue to work on your assignments in a similar environment to OS1 and not have to have a full virtual machine running!

_*Note:*_ when a "host" is referenced, this is in regard to your own laptop and your own environment, not any container or virtual machine you might have running. 

### 1. Get Docker

You can [download and install Docker at this link](https://www.docker.com/get-started)

Docker creates operating system level virtualizations through "containers". It’s alot like a traditional Virtual Machine, but containers are run through the host system kernel while maintaining their own software libraries and configurations. In short, containers are significantly less expensive as they don't have to spin up their own virtual kernels. 

### 2. Start docker

Once you've installed Docker, fire it up. It will run in the background and give you access to its command line tools. 

### 3. Pull the CentOS image

Grab the CentOS image with the following command: 
```bash
docker pull centos
```
An image is a template "snapshot" used to build containers. Images contain the specific configurations and packages that define what a container is. 

### 4. Start the container
```bash
docker run -i -t centos
```
This will bring up the CentOS container in interactive mode with the CentOS command line. There are a huge number of flags for running containers, but this is an easy way to directly gain access to the CentOS command line.

Here is the [docker reference](https://docs.docker.com/engine/reference/run/) for flags and running an image. 

### 5. Install dev dependencies

Because this CentOS image is a bare bone, fresh start, linux distro with nothing on it, you will need to install a few unix dev tools. This can easily be done with the following command:
```bash
yum groupinstall "Development tools"
```

To install Vim:
```bash
yum install vim
```
If you find that you're missing some tool, try searching online for the install command (make sure to specify CentOS when googling). It is likely a yum command that you're looking for. 

### 6. Place files onto container

#### Using SCP
You can pull down your files from a server with this command:

```bash
scp username@access.engr.oregonstate.edu:~/path/to/smallsh.zip /path/to/destination
```

#### Using Docker cp

If you have files on your local host machine that you want on the docker container, you can use the built in docker cp command on your host machine:

```
docker cp path/to/file/testing.txt <container name>:/path/to/destination
```

This might look something like this:
```
docker cp path/to/testing.txt wizardly_montalcini:/path/to/target
```

_Note:_ the container needs to be running for this to work!

To find the running container name, use the following command on your host machine:
```
docker container ls
```

This will show us something like this. We can find the name on the far right:
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
4218f505a811        centos              "/bin/bash"         2 minutes ago       Up 2 minutes                            wizardly_montalcini
```

### 7. Work, Compile, Run

Now that you have the container running, installed the development dependencies, and pulled your files, you can proceed normally! Work on your program with vim, compile, and run your executable as you would on OS1.

Here's an example of me doing this on a CentOS docker container
```bash
[root@f30ebeebacde /]# make
gcc -Wall -c smallsh.c buffer_io.c utility.c
gcc -Wall -o smallsh smallsh.o buffer_io.o utility.o
[root@f30ebeebacde /]# ./smallsh
:ls
README.md      buffer_io.c  etc   lib64         media  proc  sbin       smallsh.c  srv  usr        var
anaconda-post.log  buffer_io.o  home  makefile          mnt    root  shelltest  smallsh.h  sys  utility.c
bin        dev      lib   mcbridej_smallsh.zip  opt    run   smallsh    smallsh.o  tmp  utility.o
:cat README.md
# Smallsh
Author: John McBride
...
```

### 8. Get files off container
Once you are ready to get your files back, you can use SCP or the built in docker cp command. These are similar to putting your files onto the container, but with the paths switched.

#### Using SCP
```bash
scp /path/to/file.txt username@access.engr.oregonstate.edu:~/path/to/target
```

#### Using docker cp 
On your local host:
```bash
docker cp <containerId>:/file/path/within/container  /host/path/destination
```

### 9. Using Docker volumes
There is a better way to get files on and off your container, but it's slightly more complicated. In this example, let's mount a file system _volume_. You can [read all about volumes](https://docs.docker.com/storage/volumes/) and how they are defined by docker. But the quick and dirty way to get files on to a container from your host when you start docker is as follows:

```bash
$ docker run -it -v "/host/user/folder/to/mount:/container/destination" centos 
```

Note the new `-v` flag followed by a full file path mapping. Let's break it down. The `-v` command tells docker to mount a volume. The first part of the path preceding the `:` defines the source directory in the host's filesystem to mount. The path after the `:` defines the destination inside the container to mount the directory!

Now, when you poke around the container, the files from the source folder will be in the destination folder. The really cool thing about this is that files are persisted across volumes. Or in other words, if you change a file that's been mounted by a volume, it will also be changed on host! This eliminates the need for copy files and folders to and from the container!

_Much thanks to [Nathan](https://github.com/nathanperkins) for pointing out this tidbit!_ 


### 10. Exiting the container 

You can exit and stop a container in interactive mode with `Ctrl d`

You can detach from a container when in interactive mode with `Ctrl p` `Ctrl q`. To re-attach to the container, use the `docker attach` command:

```bash
docker attach <container name>
```

This would be something like this:
```bash
docker attach wizardly_montalcini
```

If you need to kill a container, you can use the `docker kill` command:
```bash
docker kill <container name>
```

Using our example, this would look like this:
```bash
docker kill wizardly_montalcini
```
Warning! Containers are NOT persistent. Again, they are _*NOT persistent.*_ Once one is stopped or killed, you will loose everything on it. If you want to keep a container running, just detach from it or make sure to `SCP` or `docker cp` your files off the container before you kill it.

If you stop a docker container you can bring it back up with the `docker run -i -t centos` command. 

## Extras!
This section will serve as some docker extras.

### CentOS Docker Hub 
The official [CentOS Docker image](https://hub.docker.com/_/centos/) from docker hub - This has some interesting tid-bits about security dependencies and installing updates. 

### Dockerfiles
Starting the container and installing the dev dependencies every single time you start it can be kind of annoying. Thankfully, you can use dockerfiles to automate building containers. Here is a sample dockerfile that will build the centos container and install the dev dependencies for us.

```dockerfile
CMD yum groupinstall "Development tools"
```

These can get really complicated. [Here is some very useful info](https://www.digitalocean.com/community/tutorials/docker-explained-using-dockerfiles-to-automate-building-of-images) on how dockerfiles work, how to use them, and how you can make one that fits your needs.

[Here is the official](https://github.com/CentOS/CentOS-Dockerfiles) CentOS dockerfile repository on github.

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
