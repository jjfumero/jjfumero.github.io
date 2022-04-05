---
title: "Rethinking Parallel Programming APIs: Towards Searching for the Gold API"
collection: talks
type: "Invited Talk"
permalink: /talks/2021-01-25-kentUniversity2021-talk
venue: "University of Kent - Seminars "
date: 2021-01-25
location: "Virtual"
---

[Link to the event](https://www.kent.ac.uk/events/event/46790/juan-fumero)

## Abstract

Usually, parallel programming APIs, and especially APIs for GPU and FPGA computing, are designed for a specific parallel computing architecture and one particular programming language. Primary examples are (i) CUDA, which allows programmers to use the GPU for computing, and (ii) OpenCL, which allows developers to use any type of hardware accelerator, including GPUs and FPGAs. These APIs are designed for the C programming language and have been tightly coupled to the GPU computer-architecture. 

Based on these APIs for GPU computing, there are many derivatives that ease programmability for some set of programming languages. Some examples are SYCL, a standard for parallel programming using the C++ STL; Aparapi, a parallel programming framework on top of OpenCL for Java; CUDA Trust, a high-level API on top of CUDA for common parallel operations, just to name a few. However, the utilization and generalization of these APIs to hardware accelerators is still hard. Besides, many of the proposed APIs are layered on top of existing ones, making performance benefits a hard task to achieve. They usually lack expressiveness from high-level programming languages, such as Java, R, or Python, in which the combination of functional style with object-oriented programming is still challenging to implement. Additionally, existing parallel programming APIs still require high-level expertise in computing architecture, which complicates the applications' portability. 

In this talk, we rethink how APIs are built. We will first revisit the most common parallel APIs for heterogeneous architectures (e.g., GPUs and FPGAs), summarising their major design decisions, benefits, and weaknesses. We will then present our work in progress at the University of Manchester towards solving some of the presented issues, showing how we can take advantage of parallelization while keeping the user application high-level by mixing dynamic parallelism with functional,  array-oriented, data-driven, and object-oriented programming paradigms. 

