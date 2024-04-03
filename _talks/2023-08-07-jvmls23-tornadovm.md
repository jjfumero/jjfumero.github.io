---
title: "From CPU to GPU and FPGAs: Supercharging Java Applications with TornadoVM"
collection: talks
type: "Talk"
permalink: /talks/2023-08-07-jvmls23-tornadovm
venue: "JVMLS 2023 - Oracle Santa Clara, CA"
date: 2023-08-07
location: "Santa Clara, CA, US"
---

[Summary](https://jjfumero.github.io/talks/2023-08-07-jvmls23-tornadovm)


## Abstract

TornadoVM is a state-of-the-art framework that enables hardware acceleration of Java applications, leveraging CPUs, GPUs, and FPGAs to achieve high-performance gains. In this talk, we will discuss the latest developments in TornadoVM focusing mainly on the newly revamped APIs for parallel programming; the TaskGraph API and the Execution Plan API. 

The TaskGraph API allows developers to identify the Java methods and the data to be offloaded. The Execution Plan API allows developers to define optimization parameters for parallel execution. This includes specifying target hardware accelerators, optimizing thread deployment, and enabling dynamic task migration and code profiling. 


In addition to the new APIs and data types, we will also present the results of our latest work in collaboration with Intel; the development of a SPIR-V backend and integration with Intel Level Zero. The SPIR-V backend has complemented the pre-existing OpenCL and PTX ones adding more versatility and portability for developers to choose the best hardware accelerator for their specific use case. 

Finally, we will present our work-in-progress towards integrating TornadoVM with the project Panama by implementing custom off-heap data types for vectors and ND-Arrays. These new data types can be optimized by the TornadoVM runtime to use shared memory, unified memory and device memory between different heterogeneous hardware more efficiently, paving the way for advanced AI/ML on the Java platform.


The whole presentation is available on YouTube:

[![](https://markdown-videos-api.jorgenkh.no/youtube/VTzGlnv6nuA)](https://youtu.be/VTzGlnv6nuA)


### Additional Resources

* [Video](https://www.youtube.com/watch?v=VTzGlnv6nuA)
* [PDF Presentation](https://github.com/jjfumero/jjfumero.github.io/blob/master/files/presentations/TornadoVM-JVMLS23v2-clean.pdf)


### TornadoVM source code

* [TornadoVM @ GitHub](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
