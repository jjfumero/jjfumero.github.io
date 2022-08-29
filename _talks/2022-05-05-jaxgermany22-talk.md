---
title: "Boosting Performance of Java programs by Running on GPUs and FPGA via TornadoVM"
collection: talks
type: "Talk"
permalink: /talks/2022-05-05-jaxgermany22-talk
venue: "Jax Mainz 2022"
date: 2022-05-05
location: "Mainz, Germany"
---

[Summary](https://jax.de/core-java-jvm-languages/boosting-performance-of-java-programs-by-running-on-gpus-and-fpga-via-tornadovm/)

## Abstract

Heterogeneous hardware such as GPUs, multi-core CPUs, and FPGAs are present in almost every computing system today. Programmers can increase the performance of their applications by running certain types of workloads, such as Deep Learning applications, math simulation, or Fintech operations, on heterogeneous hardware. Furthermore, in near the future, more specialized hardware resources will emerge to solve a specialized set of tasks. So, there is no escape. Programmers of current and future computing systems will need to handle execution on heterogeneous computing platforms. But at what cost? Usually, heterogeneous parallel programming is only accessible from low-level programming languages, such as OpenCL, CUDA, SYCL, and onePI. ​In this talk, we will show TornadoVM, an alternative approach to automatically accelerate Java applications by transparently offloading computation on GPUs, FPGAs, and multi-core CPU systems. We will show how TornadoVM can increase the performance of Java programs of up to 100 times by running on commodity GPUs. Furthermore, we will analyze which types of applications are suitable for acceleration, how developers can benefit from TornadoVM, and discuss how it internally works to achieve this goal.

### Additional Resources

* Link: [link](https://jax.de/core-java-jvm-languages/boosting-performance-of-java-programs-by-running-on-gpus-and-fpga-via-tornadovm/)
* [Demos used in the presentation](https://github.com/jjfumero/tornadovm-examples)


### TornadoVM source code

* [TornadoVM @Github](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
