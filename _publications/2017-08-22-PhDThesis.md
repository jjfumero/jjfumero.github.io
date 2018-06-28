---
title: "PhD Thesis: Accelerating Interpreted Programming Languages on GPUs with Just-In-Time and Runtime Optimisations"
collection: publications
permalink: /publication/2017-08-22-PhDThesis
excerpt: ''
date: 2017-08-22
venue: 'The University of Edinburgh, UK'
paperurl: 'https://www.era.lib.ed.ac.uk/handle/1842/28718'
citation: 'Juan Fumero. PhD Thesis: Accelerating Interpreted Programming Languages on GPUs with Just-In-Time and Runtime Optimisations. The University of Edinburgh, UK. August 2017.'
---
### Abstract


Nowadays, most computer systems are equipped with powerful parallel devices
such as Graphics Processing Units (GPUs). They are present in almost every computer
system including mobile devices, tablets, desktop computers and servers. These
parallel systems have unlocked the possibility for many scientists and companies to
process significant amounts of data in shorter time. But the usage of these parallel
systems is very challenging due to their programming complexity. The most common
programming languages for GPUs, such as OpenCL and CUDA, are created for expert
programmers, where developers are required to know hardware details to use GPUs.


However, many users of heterogeneous and parallel hardware, such as economists,
biologists, physicists or psychologists, are not necessarily expert GPU programmers.
They have the need to speed up their applications, which are often written in high-level
and dynamic programming languages, such as Java, R or Python. Little work has
been done to generate GPU code automatically from these high-level interpreted and
dynamic programming languages. This thesis presents a combination of a programming
interface and a set of compiler techniques which enable an automatic translation
of a subset of Java and R programs into OpenCL to execute on a GPU. The goal is
to reduce the programmability and usability gaps between interpreted programming
languages and GPUs.
 

The first contribution is an Application Programming Interface (API) for programming
heterogeneous and multi-core systems. This API combines ideas from functional
programming and algorithmic skeletons to compose and reuse parallel operations.


The second contribution is a new OpenCL Just-In-Time (JIT) compiler that automatically
translates a subset of the Java bytecode to GPU code. This is combined with
a new runtime system that optimises the data management and avoids data transformations
between Java and OpenCL. This OpenCL framework and the runtime system
achieve speedups of up to 645x compared to Java within 23% slowdown compared to
the handwritten native OpenCL code.


The third contribution is a new OpenCL JIT compiler for dynamic and interpreted
programming languages. While the R language is used in this thesis, the developed
techniques are generic for dynamic languages. This JIT compiler uniquely combines
a set of existing compiler techniques, such as specialisation and partial evaluation, for
OpenCL compilation together with an optimising runtime that compile and execute R
code on GPUs. This JIT compiler for the R language achieves speedups of up to 1300x
compared to GNU-R and 1.8x slowdown compared to native OpenCL.

