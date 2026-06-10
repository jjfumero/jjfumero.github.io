---
title: "Java Code Reflection for Targeting Foreign Programming Languages"
collection: talks
type: "Talk"
permalink: /talks/2026-02-23-java-and-gpus-babylon
venue: "Seminar at The University of Salerno, Italy"
date: 2026-02-23
location: "Laboratorio ISISLab, Dipartimento di Informatica, Università di Salerno (Edificio F, Stecca 7, Lab. 10, II piano) – Via Giovanni Paolo II, 132, 84084 Fisciano (SA)"
---

[Link](https://www.isislab.it/event/seminar-java-code-reflection-for-targeting-foreign-programming-languages-by-juan-fumero/)

## Abstract

Project Babylon is a new OpenJDK initiative to extend Java’s reflection APIs that
allow reflection of Java code. This project enables developers to reflect over Java
methods and lambdas, obtain their symbolic representations (code models), and
query or transform them at runtime. With code models, developers can modify code,
apply optimizations, and translate Java into other programming models without
relying on third‑party libraries.


A key exploration area in Babylon is GPU enablement via HAT (Heterogeneous
Accelerator Toolkit), which targets CUDA and OpenCL environments. In this talk, we
will dive into Babylon’s core abstractions for inspecting and manipulating Java code
at runtime, then demonstrate how code reflection can be used to offload parallel
workloads to GPUs and enabling more efficient and hardware-accelerated
executions directly from Java.

### Slides 

- [Link](https://cr.openjdk.org/~jfumero/presentations/ProjectBabylonAndGPUs-Salerno2026.pdf)

### Project Babylon and HAT

* [OpenJDK Babylon @ GitHub](https://github.com/openjdk/babylon)
* [Optimizing GPU Programs from Java using Babylon and HAT](https://openjdk.org/projects/babylon/articles/hat-matmul/hat-matmul)
