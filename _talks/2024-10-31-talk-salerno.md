---
title: "Heterogeneous Computing from Managed Runtime Programming Languages: Opportunities and Challenges"
collection: talks
type: "Talk"
permalink: /talks/2024-10--31-talk-salerno
venue: "The University of Salerno, Italy"
date: 2024-10-31
location: "Salerno, Italy"
---

[Link](https://www.isislab.it/event/seminar-heterogeneous-computing-from-managed-runtime-programming-languages-opportunities-and-challenges-juan-fumero-university-of-manchester-intel-oneapi-innovator/)


## Abstract

Parallel programming APIs, particularly those designed for GPU and FPGA computing, are typically tailored to 
specific architectures and programming languages. Examples include (i) CUDA, which
allows developers to harness GPU computing power, and (ii) OpenCL, which provides access to a
wide range of hardware accelerators, such as GPUs and FPGAs. These APIs are predominantly built
for the C/C++ programming languages and are closely linked to GPU architectures.
However, many developers do not primarily use C/C++. According to the TIOBE index, in addition to
C and C++, popular languages in 2024 include Python, Java, and C#, along with other languages that
run on managed environments (such as the Java Virtual Machine, JVM). This raises the question: how
can developers using managed runtime languages access heterogeneous hardware like GPUs and
FPGAs? What solutions exist, and what challenges do they face in doing so?

In this talk, I will explore key features of parallel programming models and discuss the challenges
that runtime environments encounter when aiming to harness the performance of parallel hardware. I
will also present the current research conducted at The University of Manchester, which addresses
some of these challenges. This includes proposals that combine the expressiveness of parallelism
with functional programming, data-centric approaches, and object-oriented paradigms to maintain
high-level abstractions while effectively utilising parallelism.


### TornadoVM source code

* [TornadoVM @ GitHub](https://github.com/beehive-lab/TornadoVM)
* Docker images for NVIDIA GPUs and Intel Integrated GPUs: [link](https://github.com/beehive-lab/docker-tornado)
