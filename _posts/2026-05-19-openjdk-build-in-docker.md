---
title: 'Building the Latest JDK from Source with Docker'
date: 2026-05-19
permalink: /posts/2026/05/15/openjdk-build-docker
author_profile: false
tags:
 - OpenJDK
 - Build
 - Docker
excerpt: "How to build OpenJDK from source in a Docker Container"
---

The following `Dockerfile` builds the latest JDK 27 from source using OpenJDK 26 as base JDK.

```Dockerfile
FROM ubuntu:26.04

RUN apt-get update -q && apt install -qy \
        build-essential git cmake vim maven curl bash unzip zip wget

WORKDIR /opt/openjdk/
## Use JDK 26 as base
RUN wget https://download.java.net/java/GA/jdk26.0.1/458fda22e4c54d5ba572ab8d2b22eb83/8/GPL/openjdk-26.0.1_linux-x64_bin.tar.gz
RUN tar xvzf openjdk-26.0.1_linux-x64_bin.tar.gz
ENV PATH=$JAVA_HOME/bin:$PATH
ENV JAVA_HOME=/opt/openjdk/jdk-26.0.1

## Install OpenJDK Dependencies
RUN apt-get update -y
RUN apt-get install -y autoconf libfreetype6-dev
RUN apt-get install -y file
RUN apt-get install -y libasound2-dev
RUN apt-get install -y libcups2-dev
RUN apt-get install -y libfontconfig1-dev
RUN apt-get install -y libx11-dev libxext-dev libxrender-dev libxrandr-dev libxtst-dev libxt-dev

## Configure OpenJDK from source 
RUN git clone --depth 1 https://github.com/openjdk/jdk
WORKDIR /opt/openjdk/jdk
RUN bash configure --with-boot-jdk=${JAVA_HOME}
RUN make clean
RUN make images

## Update PATH and JAVA_HOME
ENV PATH=/opt/openjdk/jdk/build/linux-x86_64-server-release/jdk/bin/:$PATH
ENV JAVA_HOME=/opt/openjdk/jdk/build/linux-x86_64-server-release/jdk

## Expose a volume to pass files in the local directory
WORKDIR /opt/openjdk/jdk/
VOLUME ["/data"]
```

To build the image:

```bash
docker build . -t openjdk
```

If successful, then we can run the built `Java` as follows:


```bash
$ docker run -it openjdk java --version 
openjdk 27-internal 2026-09-15
OpenJDK Runtime Environment (build 27-internal-adhoc.root.jdk)
OpenJDK 64-Bit Server VM (build 27-internal-adhoc.root.jdk, mixed mode)
```

We can also run any Java program using the volume `data` configured in the image:

```bash
docker run -it -v $PWD:/data -w /data openjdk java MyProgram.java 
```


