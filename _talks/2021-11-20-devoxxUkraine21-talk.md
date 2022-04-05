---
title: "TornadoVM: Transparent Hardware Acceleration for Java…and Beyond!"
collection: talks
type: "Talk"
permalink: /talks/2021-11-20-devoxxUkraine21-talk
venue: "Devoxx Ukraine 2021"
date: 2021-11-20
location: "Virtual"
---

[Video](https://www.youtube.com/watch?v=HxhO4hMKi5M)

## Abstract

The proliferation of heterogeneous hardware in recent years means that every system we program is likely to include a mix of computing elements; each of these with different hardware characteristics that enable programmers to improve performance while decreasing energy consumption. These new heterogeneous devices include multi-core CPUs, GPUs, and FPGAs. This trend has been followed by changes in software development norms that do not necessarily favour programmers.

A prime example is the two most popular heterogeneous programming languages, CUDA and OpenCL, which expose several low-level features to the API, making them difficult to use by non-expert users. Instead of using low-level programming languages, developers in industry and academia tend to use higher-level, object-oriented programming languages, typically executed on managed runtime environments, such as Java, R, Python, and Javascript. Although many programmers might expect that such programming languages would have already been adapted for transparent execution on heterogeneous hardware, the reality is that their support is either very limited or absent.

In this talk, we present TornadoVM, a plug-in for OpenJDK that allows JVM programmers to automatically run their applications on multi-core CPUs, GPUs, and FPGAs. Furthermore, TornadoVM performs task-migration from one device to another at runtime, entirely transparent for the user. In this talk, we will summarize the TornadoVM project, and we will explain, via examples, how TornadoVM optimizes, offloads and executes applications on heterogeneous hardware, including GPUs and FPGAs for different backends, such as OpenCL, NVIDIA PTX and the brand new SPIR-V backend.


### Additional Resources

* Link to the recording: [link](https://www.youtube.com/watch?v=HxhO4hMKi5M)
* [Slides](https://github.com/jjfumero/jjfumero.github.io/blob/master/files/presentations/TornadoVM-DevoxxUkraine2021.pdf)
* [Demos used in the presentatiopn](https://github.com/jjfumero/qconplus2021-tornadovm)


### TornadoVM source code

* [TornadoVM @Github](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
