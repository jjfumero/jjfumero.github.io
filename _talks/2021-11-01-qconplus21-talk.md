---
title: "Level Up Your Java Performance with TornadoVM"
collection: talks
type: "Talk"
permalink: /talks/2021-11-01-qconplus21-talk
venue: "QCon Plus 2021, Virtual"
date: 2021-11-01
location: "Virtual"
---

[Video](https://plus.qconferences.com/plus2021/presentation/level-your-java-performance-tornadovm) [Summary](https://www.infoq.com/articles/java-performance-tornadovm/)


## Abstract

Heterogeneous hardware such as Graphics Processing Units (GPUs) and Field Programmable Gate Arrays (FPGAs) are widely used for specific domains of applications such as Machine Learning, Data Science, Numerical Analytics and Fintech, due to the offer of a higher level of performance.

The increased performance, however, comes at the cost of programmability since different programming models and frameworks are required to program different classes of devices (GPUs, FPGAs, CPUs), thereby leading to code fragmentation. This makes the programmability of these devices more difficult to develop, and harder to understand and maintain, especially from high-level programming languages such as Java.

This talk will give an overview of the TornadoVM project, a parallel programming framework, and a Virtual Machine for transparently offloading Java programs onto GPUs and FPGAs. We will explain why and when it is beneficial to run on heterogeneous hardware. Besides, we will explain how developers can program and benefit from the GPUs and FPGAs processing power from Java using TornadoVM. Finally, we will give an overview regarding how TornadoVM compiles, optimizes and selects the best possible device for execution for different backends, totally transparent to the user. 

### Additional Resources

* Link to the recording: [link](https://plus.qconferences.com/plus2021/presentation/level-your-java-performance-tornadovm)
* [Demos used in the presentatiopn](https://github.com/jjfumero/qconplus2021-tornadovm)
* Article for InfoQ: [link](https://www.infoq.com/articles/java-performance-tornadovm/)


### TornadoVM source code

* [TornadoVM @Github](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
