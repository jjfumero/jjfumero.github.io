---
title: "Designing Parallel Programming APIs for Heterogeneous Hardware on top of Managed Runtime Systems"
collection: talks
type: "Talk"
permalink: /talks/2024-03-13-ull-apidesign
venue: "University of La Laguna, Tenerife, Spain"
date: 2024-03-13
location: "ULL, Tenerife, Spain"
---

[Summary](https://jjfumero.github.io/talks/2024-03-13-ull-apidesign)


Talk given at the University of La Laguna, Tenerife, Spain as a seminar for the master course in Parallel Computing. 

Date: `2024-03-13`

## Abstract

Parallel programming APIs, especially those for GPU and FPGA computing, are designed for specific domain architectures and particular programming languages. Some examples include (i) CUDA, which enables programmers to utilize the GPU for computing, and (ii) OpenCL, which allows developers to utilize any type of hardware accelerator, including GPUs and FPGAs. These APIs are tailored for the C/C++ programming language and have been closely tied to GPU computer architecture.

However, not all developers use C/C++ as their primary language and programming environment. For instance, according to the TIOBE index, apart from C and C++, the most popular programming languages in 2024 include Python, Java, C#, among many other languages managed on runtime environments (such as the Java Virtual Machine, JVM). But how do developers using runtime environments access heterogeneous hardware? What options are available, and what are the challenges in accessing GPUs and FPGAs?

In this talk, we will review the most relevant characteristics of parallel programming models and describe the challenges that runtime environments must face to leverage and exploit acceleration and performance in parallel hardware. Finally, we will present our work in progress at the University of Manchester to address some of the issues presented, showcasing proposals on how we can harness parallelism while maintaining user application at a high level by combining the expressiveness of parallelism with functional programming paradigms, data-oriented approaches, and object-oriented paradigms.


### Additional Resources

* [PDF Presentation](https://github.com/jjfumero/jjfumero.github.io/blob/master/files/presentations/ParallelAPIs-Design.pdf)


### TornadoVM source code

* [TornadoVM @ GitHub](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
