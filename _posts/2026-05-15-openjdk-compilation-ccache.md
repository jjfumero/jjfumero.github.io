---
title: 'How to fix ccache error when building OpenJDK from source'
date: 2026-05-15
permalink: /posts/2026/05/15/openjdk-compilation-ccache
author_profile: false
tags:
 - OpenJDK
 - Build
excerpt: "Provide CC and CXX env variables to the OpenJDK build with the --enable-ccache option."
---

When compiling OpenJDK from source and have [`ccache`](https://ccache.dev/) enabled, we might get the following error:

```bash
configure: Using default toolchain gcc (GNU Compiler Collection)
checking for gcc... /usr/lib64/ccache/gcc
checking resolved symbolic links for CC... /usr/bin/ccache
configure: Please use --enable-ccache instead of providing a wrapped compiler.
configure: error: /usr/lib64/ccache/gcc is a symbolic link to ccache. This is not supported.
configure exiting with result code 1
```

This usually happens when the `gcc` found in your `PATH` is not the actual GCC binary, but a `ccache` wrapper:

```bash
$ which gcc
/usr/lib64/ccache/gcc

$ ll /usr/lib64/ccache/gcc
lrwxrwxrwx. 1 root root 16 May 12 15:27 /usr/lib64/ccache/gcc -> ../../bin/ccache
```

In this case, `/usr/lib64/ccache/gcc` is a symbolic link to the `ccache` executable. OpenJDK's build system does not support passing a wrapped compiler directly. 

A possible solution to this [without manipulating the system files](https://ccache.dev/manual/4.13.6.html#_run_modes) is to set the `CC` and `CXX` env variables with the right location for the C/C++ binaries:

```bash
bash configure \
  CC=/usr/bin/gcc \
  CXX=/usr/bin/g++ \
  --with-boot-jdk=$JAVA_HOME \
  --with-jtreg=$JTREG_HOME
```

Furthermore, we can also enable `ccache` with the `--enable-ccache` in combination with the `CC` and `CXX` env variables:

```bash
bash configure \
  CC=/usr/bin/gcc \
  CXX=/usr/bin/g++ \
  --enable-ccache \
  --with-boot-jdk=$JAVA_HOME \
  --with-jtreg=$JTREG_HOME
```

### Links

- [Mail.jdk thread](https://mail.openjdk.org/archives/list/build-dev@openjdk.org/message/AHTW4GBHWOSWC5U2TMXDA4OCPUUL5HDG/)






