---
title: "TornadoVM: Multi-Backend Hardware Acceleration Framework for Java"
collection: talks
type: "Talk"
permalink: /talks/2023-03-14-oneAPI-Lang-SIG-talk
venue: "oneAPI Language SIG - March 2023"
date: 2023-03-14
location: "Online"
---

## Abstract

In this talk, we will explain why we need TornadoVM, what it is and how developers can use it. 
For the oneAPI Language SIG Group, we will focus on the abstract software components of TornadoVM for representing tasks graphs, manage compilation and manage execution on heterogeneous hardware. 
The goal of this talk is to understand the internal components of TornadoVM that communicate with differnet backends (e.g., OpenCL, SPIR-V, and CUDA), and how this components can be reused by other managed runtime systems. 

### Additional Resources

* [Meeting notes](https://github.com/oneapi-src/oneAPI-tab/tree/main/language)
* [Presentation](https://github.com/oneapi-src/oneAPI-tab/blob/main/language/presentations/2023-03-14-TornadoVM.pdf)
* [Blog post](https://jjfumero.github.io/posts/2022/09/tornadovm-internal-apis/)


### TornadoVM source code

* [TornadoVM @ GitHub](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
