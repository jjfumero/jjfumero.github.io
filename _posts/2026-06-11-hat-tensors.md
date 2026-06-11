---
title: 'Exploiting GPU Tensor Cores from Java using Babylon'
date: 2026-06-11
permalink: /posts/2026/06/11/hat-tensors
author_profile: false
tags:
  - Babylon
  - GPU
  - HAT
  - Java
  - OpenJDK
excerpt: "Proof of concept enabling Tensor Core Programming from Java"
---


I’ve just published a new technical article proposing an extension to OpenJDK Project Babylon and HAT. The post explores
how to unlock GPU Tensor Cores directly from Java for supported hardware, while ensuring cross-platform portability by
mapping tensors to loop-tiles for parallel processing on devices without explicit tensor instructions.

I also walk through the API choices, as well as compiler and runtime integration, showing how we go from high-level Java
straight to emitting explicit NVIDIA HMMA instructions. Besides, this article show how Java applications can be tuned
for GPUs using HAT and NVIDIA profilers. 🚀

### Abstract 

Tensor Cores are dedicated hardware on NVIDIA GPUs that can be programmed to accelerate matrix-multiply-accumulate (MMA)
operations. Running MMA operations on these cores can increase performance of specific applications dramatically.
However, NVIDIA tensor cores are only available for NVIDIA GPUs and exposed to the CUDA programming model through
low-level APIs.

Ideally, we would also like to make those operations accessible from Java to accelerate domain-specific workloads (e.g.,
LLMs), but those operations must be portable across accelerators. MMA capabilities are also available for other
computing platforms such as Apple devices using the Metal programming model, or Intel XPUs via the OpenCL and oneAPI
software stacks. However, these operations are not always achievable for other programming models such as OpenCL 1.2 (
the OpenCL version that Apple supports), which emphasizes the need for portability. This article tackles the
architectural specificity of NVIDIA Tensor Cores by exploring a portable approach to tensor operations across multiple
hardware accelerators that can be used from Java.

The goal of this article is twofold. First, we show that Java programs can reach close-to-native performance for
matrix-multiply computations on hardware with accelerated MMA support, such as NVIDIA GPUs. Second, we study how the
same Java Tensor API can be mapped across different parallel programming models and vendors while remaining portable for
both, source code and runtime scheduling parameters.

To support this approach, we extended the Heterogeneous Accelerator Toolkit (HAT), a parallel programming framework to
accelerate data-parallel workloads on hardware accelerators, with a tensor-aware API and a set of code transformations
using the code reflection API from the OpenJDK Project Babylon.

Finally, we evaluate the performance of the system using the HAT Tensor API from Java in the context of two GPU
platforms, an Apple M4 Max GPU and an NVIDIA Ampere A10 GPU. We show that, by enabling tensor cores on supported
hardware (NVIDIA), we can speed up the naïve matrix multiplication kernel from 240 GFLOP/s to 7.3 TFLOP/s, while the
application remains portable to run on Apple M4 GPU via OpenCL 1.2, where with some parameter tuning, we can increase
performance by 8x over the naïve matrix-multiplication.

----------
🔗 Link to the full article: [link](https://openjdk.org/projects/babylon/articles/hat-tensors/hat-tensors)

----------

### Current Status

This article shows an approach to extend the HAT programming model with an API for explicit tensor-core programming.
Furthermore, it shows how to make this approach generic to be able to process computations expressed with the proposed
HAT tensor core API on accelerators without explicit tensor instructions. While this article shows a complete approach,
the final integration into the HAT programming model is under discussion.