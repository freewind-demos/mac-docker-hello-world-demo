Mac Docker Hello World Demo
===========================

在Mac下使用Docker要比Ubuntu等Linux系统麻烦一些，主要是因为Docker需要的一些操作系统层面的支持Mac不具备（Mac是BSD的一种）。
所以还需要先安装一个`docker-machine`，它利用了Virtual Box虚拟机，在Mac上先运行一个Linux虚拟机，然后在它里面执行docker命令。

这是一个关键性的区别，需要注意。

安装
---

```
brew install docker-machine
brew install docker
```

创建docker machine
----------------

```
docker-machine create default
```

输出：

```
$ docker-machine create default
Creating CA: /Users/freewind/.docker/machine/certs/ca.pem
Creating client certificate: /Users/freewind/.docker/machine/certs/cert.pem
Running pre-create checks...
(default) Image cache directory does not exist, creating it at /Users/freewind/.docker/machine/cache...
(default) No default Boot2Docker ISO found locally, downloading the latest release...
(default) Latest release for github.com/boot2docker/boot2docker is v18.06.1-ce
(default) Downloading /Users/freewind/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v18.06.1-ce/boot2docker.iso...
(default) Creating VirtualBox VM...
(default) Creating SSH key...
(default) Starting the VM...
(default) Check network to re-create if needed...
(default) Found a new host-only adapter: "vboxnet0"
(default) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env default
```

这里有两点需要注意：

1. 它需要下载一个`boot2docker.iso`到本地，有可能被墙，也可能很慢，需要自己科学解决
2. `(default) Waiting for an IP...`：在这一步可能等待的时间较长，我这里等了约2分钟

查看docker-machine环境信息：

```
$ docker-machine env default
```

输出：

```
set -gx DOCKER_TLS_VERIFY "1";
set -gx DOCKER_HOST "tcp://192.168.99.100:2376";
set -gx DOCKER_CERT_PATH "/Users/freewind/.docker/machine/machines/default";
set -gx DOCKER_MACHINE_NAME "default";
# Run this command to configure your shell:
# eval (docker-machine env default)
```

由于我使用的是fish，所以这里出来的是fish脚本。
它最后提示说我们需要在shell中运行`eval (docker-machine env default)`，把这些变量放到环境中：

```
eval (docker-machine env default)
```

最好把这句话加入到shell的启动脚本中，不然每次打开一个新的shell，都需要运行一遍，否则`docker`命令就会报错。

运行Hello World
-------------

到了docker命令时，它的表现不论在Mac还是Linux下，都是一样的了。

```
docker run hello-world
```

它会下载`library/hello-world`并运行：

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9db2ca6ccae0: Pull complete
Digest: sha256:4b8ff392a12ed9ea17784bd3c9a8b1fa3299cac44aca35a85c90c5e3c7afacdc
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

可以看到最后输出的`Hello from Docker!`，说明正常。

它同时还说明了这个信息是怎么打出来的：

```
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```

以及更多信息：

```
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

执行docker容器里的命令
--------------

在前面的更多信息中，它提到了可以使用`run -it`命令，执行docker容器里的命令：

```
docker run -it ubuntu bash
```

它将会下载最新的`ubuntu:latest`，然后进入它的bash:

```
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
124c757242f8: Pull complete
9d866f8bde2a: Pull complete
fa3f2f277e67: Pull complete
398d32b153e8: Pull complete
afde35469481: Pull complete
Digest: sha256:b5309340de7a9a540cf6c0cba3eabdfb9c9bc5153026d37991fd0028180fc725
Status: Downloaded newer image for ubuntu:latest
root@a03dc7582e87:/#
```

注意到最后一行，我们已经进入了该ubuntu容器！可以执行更多命令了：

```
# uname --all
```

输出为：

```
Linux a03dc7582e87 4.4.0-128-generic #154-Ubuntu SMP Fri May 25 14:15:18 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

