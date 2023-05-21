---
title: "Experiences in Building a Composable and Functional API for Runtime SPIR-V Code Generation"
collection: publications
permalink: /publication/2023-05-16-SPIRV-Toolkit
excerpt: ''
date: 16/05/2023
venue: 'Preprint'
paperurl: ''
citation: 'Fumero, J., Rethy, G., Stratikopoulos, A., Foutris, N., & Kotselidis, C. (2023). Experiences in Building a Composable and Functional API for Runtime SPIR-V Code Generation. ArXiv. /abs/2305.09493' 
---

### Abstract

This paper presents the Beehive SPIR-V Toolkit; a framework that can automatically generate a Java composable and functional library for dynamically building SPIR-V binary modules. The Beehive SPIR-V Toolkit can be used by optimizing compilers and runtime systems to generate and validate SPIR-V binary modules from managed runtime systems, such as the Java Virtual Machine (JVM). Furthermore, our framework is architected to accommodate new SPIR-V releases in an easy-to-maintain manner, and it facilitates the automatic generation of Java libraries for other standards, besides SPIR-V. The Beehive SPIR-V Toolkit also includes an assembler that emits SPIR-V binary modules from disassembled SPIR-V text files, and a disassembler that converts the SPIR-V binary code into a text file, and a console client application. To the best of our knowledge, the Beehive SPIR-V Toolkit is the first Java programming framework that can dynamically generate SPIR-V binary modules.

To demonstrate the use of our framework, we showcase the integration of the SPIR-V Beehive Toolkit in the context of the TornadoVM, a Java framework for automatically offloading and running Java programs on heterogeneous hardware. We show that, via the SPIR-V Beehive Toolkit, the TornadoVM is able to compile code 3x faster than its existing OpenCL C JIT compiler, and it performs up to 1.52x faster than the existing OpenCL C backend in TornadoVM.


Pre-print: [PDF](https://arxiv.org/pdf/2305.09493)
