---
title: Jenkins with DinD
categories: [Other]
tags: [Jenkins, Docker]
date: 2022-04-12 17:04:25
---

---

Inspired by <https://www.tiuweehan.com/blog/2020-09-10-docker-in-jenkins-in-docker/>

**DinD** 即 Docker in Docker，就是在 Docker 容器中运行 Docker 服务。

使用 Docker 镜像部署 Jenkins 很方便，一个简单的 `docker-compose.yml` 如下：

```yaml
version: "3"
services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    ports:
      - 9095:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home
```

<!--more-->

## 容器化的 Jenkins 带来的问题

有了 Jekins Docker 镜像，就可以一键部署多台 Jenkins 实例，但容器化的 Jenkins 在提供便利的同时也带来一些问题，例如: 如何在 Jenkins 中使用 Docker ?

解决方式搜集到的有三种：

- 在 Jenkins 镜像中安装 Docker（这也是一种 DinD）
- 共享宿主机的 docker daemon
- 使用 DinD 镜像

## Jenkins 镜像中安装 Docker

这个方式需要基于官方的 [Jenkins](https://hub.docker.com/r/jenkins/jenkins) 镜像构建自己的 Jenkins with Docker 镜像：

```dockerfile
FROM jenkins/jenkins:lts
 
 USER root
 
 RUN apt-get update && apt-get install -y  \
     ca-certificates \
     curl \
     gnupg \
     lsb-release
 
 RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
 
 RUN echo \
   "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
   $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
 
 RUN apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io
 
 
 RUN usermod -aG docker jenkins
 
 ENTRYPOINT ["/bin/sh", "-c", "service docker start && /sbin/tini -- /usr/local/bin/jenkins.sh"]
```

构建镜像：

```shell
docker build -t docker-dind:1.0 .
```

run

```shell
docker run --rm -v jenkins_home:/var/jenkins_home -p 9096:8080 --privileged jenkins-dind:1.0
```

如果你想使用 docker-compose, 这里是示例：

```yaml
version: "3"
services:
  jenkins:
    user: root
    image: jenkins-dind:1.0
    container_name: jenkins_1
    privileged: true
    ports:
      - 9096:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home
```



架构如图：

![solution-1](https://www.tiuweehan.com/img/solution-1.png)

这种 Docker 容器里安装 Docker 的方法，有点 hack 的感觉，文章里提到的缺点就是：

1. 需要额外构建一个镜像
2. 需要 `privileged` 这样高级权限(有这个权限，容器内可以直接使用宿主机资源)，不安全
3. 覆盖了父镜像的 `Entrypoint` 指令(自己构建镜像，并且加了 docker 服务，只能覆盖)
4. 手动在 Dcoker 容器里安装 Docker 有点 hack，不太推荐

## 共享宿主机的 docker daemon

这种方式比较常用，由于 Docker 服务也是一个 CS 架构，一个 docker daemon 可以通过 `unix://`、`tcp://` 还有 `ssh://` 三种协议来提供给远程客户端使用，现在只演示下使用 unix socket 的方式。

使用 `unix://` 协议共享 docker 思路很简单，把宿主机上的 `/var/run/docker.sock` 挂载到 jenkins 容器里就行，并且由于 jenkins 里容器缺少客户端，所以通常一并将宿主机的可执行文件 `docker` 一并挂载到容器里，为了避免权限问题，jenkins 使用 root 用户启动，docker-compose.yml 示例：

```yaml
version: "3"
 services:
   jenkins_2:
     image: jenkins/jenkins:lts
     user: root
     volumes:
       - ./jenkins_home:/var/jenkins_home
       - /var/run/docker.sock:/var/run/docker.sock
       # 由于我的宿主机是 OSX，所以下载的 linux 的docker，用来挂载
       - ./docker/docker:/usr/local/bin/docker
     ports:
       - 9097:8080
```

架构：

![solution-2](https://www.tiuweehan.com/img/solution-2.png)

用这个方式的缺点是：

1. Jenkins 容器需要依赖宿主机的 docker，不能独立运行
2. 需要配置好权限来连接宿主机的 docker.sock

## 使用 DinD 镜像

使用官方的 DinD 镜像来独立部署 Jenkins 专用的 Docker 服务，这种方式文章作者和我都比较喜欢，docker-compose.yml 示例如下：

```yaml
version: "3"
 
 services:
   jenkins:
     image: jenkins/jenkins:lts
     user: root
     volumes:
       - ./jenkins_home:/var/jenkins_home
       - ./docker/docker:/usr/bin/docker
     ports:
       - 9091:8080
     restart: always
     environment:
       # 使用 tcp:// 协议访问远程 docker
       DOCKER_HOST: 'tcp://dind:2375'
     networks:
       - jenkins_dind
 
   dind:
     image: docker:dind
     user: root
     privileged: true
     container_name: dind
     restart: always
     volumes:
       # 服务端和客户端共享工作目录，避免客户端 docker run 绑定的目录客户端没有的问题
       - ./jenkins_home:/var/jenkins_home
     expose:
       - 2375
     networks:
       - jenkins_dind
     environment:
       DOCKER_TLS_CERTDIR: ""
 
 networks:
   jenkins_dind:
     driver: bridge
```

需要注意的是，客户端执行 `docker build` 时，build 目录会打包上传到 docker daemon，没有什么问题，但是如果客户端执行 `docker run` 时绑定了客户端的某个目录，由于服务端没有这个目录，就会出错，所以这个方案的好处就是，可以把工作目录同时绑定到 Jenkins 和 DinD 容器里，实现共享。

架构如下：

![solution-3](https://www.tiuweehan.com/img/solution-3.png)

缺点当然是有的：

1. 需要多跑个容器，不过至少 Jenkins 可以做到完全独立部署了

完。
