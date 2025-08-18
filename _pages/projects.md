---
title: " { projects } "
permalink: /projects/
author_profile: true
---

If any of these projects interest you and you’d like to connect, please feel free to email me.

## OpenJDK/Babylon 

Working on the new code-reflection API for GPU code generation.

[https://github.com/openjdk/babylon/tree/code-reflection](https://github.com/openjdk/babylon/tree/code-reflection)


## TornadoVM: Heterogeneous Virtual Machine 

October 2017 - July 2025

A brand new Virtual Machine for Heterogeneous Computing developed at The University of Manchester. It makes use of the new Graal compiler for running Java programs on multi-core CPUs, GPUs and FPGAs via OpenCL. Compilation and execution on heterogeneous devices are transparent to the users.

TornadoVM is open-source, and it is fully available at [https://github.com/beehive-lab/TornadoVM](https://github.com/beehive-lab/TornadoVM)


## SPIR-V Beehive Toolkit 

2021 - July 2025

This is a prototype written in Java to disassemble and assemble SPIR-V binary modules. It provides a Java programming framework to allow client Java applications to assemble binary modules and dissasemble binary modules into text. More information on the format of SPIR-V can be found here:  

[https://github.com/beehive-lab/spirv-beehive-toolkit](https://github.com/beehive-lab/spirv-beehive-toolkit)


## Level Zero JNI Programming Interface 

2021 - July 2025

Baremetal GPU and FPGA programming for Java using the LevelZero API. This project is a Java Native Interface (JNI) binding for Intel's Level Zero. This library is as designed to be as closed as possible to the LevelZero API for C++.

[https://github.com/beehive-lab/levelzero-jni](https://github.com/beehive-lab/levelzero-jni)


## ProtonVM 

2020 - July 2025

ProtoVM is a tiny Parallel Java Bytecode Interpreter (BC) implemented in OpenCL capable of executing on heterogeneous architectures (multi-core CPUs, GPUs and FPGAs).

ProtonVM is a proof-of-concept for running a subset of the Java bytecodes in C++ and OpenCL. The bytecodes defined correspond to an extension of the small-subset of Java bytecode explained by Terence Parr, from the University of San Francisco of how to build a simple Virtual Machine. This project extends this simple bytecode interpreter to study the feasibility of running, as efficiently as possible, parallel bytecode interpreters on heterogeneous computer architectures.


[https://github.com/beehive-lab/ProtonVM](https://github.com/beehive-lab/ProtonVM)

## FastR-OpenCL Compiler: R JIT compiler and runtime for GPUs

March 2016 - August 2016

Using the Marawacc compiler framework (see below), I extended the existing OpenCL JIT compiler and the Partial Evaluation (in GraalVM) to allow compiler developers automatic GPU JIT compilation and execution of the R dynamic typed language. This project was part of my PhD.

This compiler is now open-source and it is available at [https://github.com/jjfumero/fastr-gpu](https://github.com/jjfumero/fastr-gpu).


## Marawacc: Java JIT compiler for GPUs

January 2014 - August 2017

Marawacc is a compiler framework for executing Java applications on GPUs automatically.  
It is composed of an API for parallel and heterogeneous programming (we called it JPAI), a JIT compiler that transforms, at runtime, Java bytecode intro OpenCL, and a runtime system that optimises the code and efficiently manages data transformations between Java and OpenCL. This project was part of my PhD.

This project is now open-source and it is available at [https://github.com/jjfumero/marawacc](https://github.com/jjfumero/marawacc)


## FastR + Apache Flink: R for distributed computing

June - November 2015

FastR + Flink is a project I work on during my internship at Oracle Labs. We connected the FastR implementation (R within Truffle+Graal) and Apache Flink, allowing to execute distributed stream and batch data processing via R. This is now an open source project:


This project is open-source, and it is available at [https://bitbucket.org/allr/fastr-flink](https://bitbucket.org/allr/fastr-flink)


## Vectorization with Haswell and CilkPlus at CERN 

June-September 2013

With the release of the Intel Sandy Bridge processor, vectorization ceased to be a “nice to have” feature and became a necessity. This work is focused on optimization, running comparative measurements of available vectorization technologies currently under investigation by the CERN Concurrency Forum. In particular, the project involves an assessment of the limits of autovectorization in two compilers, an evaluation of CilkPlus as implemented in ICC/GCC and an evaluation of AVX/AVX2 benefits with respect to legacy SSE workloads.

Unfortunatelly, this project is not open-source. :-( 


## [Master Project] yacf (Yet Another Compiler Framework) 

Source to Source compiler which parses C code with OpenMP and OpenACC directives and produce CUDA and OpenCL C kernels. This source to source also generates calls to our runtime (Frangollo), which manages memory and the execution between multiple devices. 

This project is open-source and available at [https://code.google.com/archive/p/yacf/](https://code.google.com/archive/p/yacf/)



