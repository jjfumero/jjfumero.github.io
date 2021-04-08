---
title: "Transparent Compiler and Runtime Specializations for Accelerating Managed Languages on FPGAs"
collection: publications
permalink: /publication/2021-03-23-PROGRAMMING.md
excerpt: ''
date: 23/03/2021
venue: 'The Art, Science, and Engineering of Programming (Programming) 2021'
paperurl: ''
citation: 'Michail Papadimitriou, Juan Fumero, Athanasios Stratikopoulos, Foivos Zakkak and Christos Kotselidis. Transparent Compiler and Runtime Specializations for Accelerating Managed Languages on FPGAs. Programming 2021.' 
---

### Abstract

Michail Papadimitriou, Juan Fumero, Athanasios Stratikopoulos, Foivos Zakkak and Christos Kotselidis

In recent years, heterogeneous computing has emerged as the vital way to increase computers? performance and energy efficiency by combining 
diverse hardware devices, such as Graphics Processing Units (GPUs) and Field Programmable Gate Arrays (FPGAs). The rationale behind this trend
is that different parts of an application can be offloaded from the main CPU to diverse devices, which can efficiently execute these parts as
co-processors. FPGAs are a subset of the most widely used co-processors, typically used for accelerating specific workloads due to their 
flexible hardware and energy-efficient characteristics. These characteristics have made them prevalent in a broad spectrum of computing systems 
ranging from low-power embedded systems to high-end data centers and cloud infrastructures.


However, these hardware characteristics come at the cost of programmability. Developers who create their applications using high-level programming
languages (e.g., Java, Python, etc.) are required to familiarize with a hardware description language (e.g., VHDL, Verilog) or recently heterogeneous 
programming models (e.g., OpenCL, HLS) in order to exploit the co-processors? capacity and tune the performance of their applications. Currently, 
the above-mentioned heterogeneous programming models support exclusively the compilation from compiled languages, such as C and C++. Thus, the 
transparent integration of heterogeneous co-processors to the software ecosystem of managed programming languages (e.g. Java, Python) is not seamless.
In this paper we rethink the engineering trade-offs that we encountered, in terms of transparency and compilation overheads, while integrating FPGAs 
into high-level managed programming languages. We present a novel approach that enables runtime code specialization techniques for seamless and 
high-performance execution of Java programs on FPGAs. The proposed solution is prototyped in the context of the Java programming language and 
TornadoVM; an open-source programming framework for Java execution on heterogeneous hardware. Finally, we evaluate the proposed solution for FPGA 
execution against both sequential and multi-threaded Java implementations showcasing up to 224x and 19.8x performance speedups, respectively, and 
up to 13.82x compared to TornadoVM running on an Intel integrated GPU. We also provide a break-down analysis of the proposed compiler optimizations
for FPGA execution, as a means to project their impact on the applications? characteristics.

* Pre-print: [link](https://arxiv.org/abs/2010.16304)

* Video recording: [link](https://www.youtube.com/watch?v=D1tStuiKwnU)
