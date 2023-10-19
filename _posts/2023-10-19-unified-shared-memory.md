---
title: 'Unified Shared Memory: Friend or Fue?'
date: 2023-10-19
permalink: /posts/2023/10/unified-shared-memory
author_profile: false
tags:
  - Unified Shared Memory
  - Research
  - Academic Paper
  - MaxineVM
  - Level Zero
  - CUDA 
  - Unified Memory
excerpt: "Unified Shared Memory: Friend or Fue? Understanding the Implications of Unified Memory on Managed"
---


## Key Takeaways 

- Programming heterogeneous computing systems from managed runtime systems like Java imposes many challenges. One of the biggest challenges is regarding the managed memory management between the JVM and the accelerator memory (e.g., GPU memory, which is not managed by default).
- The disjoint memory handlers between the Java process and the GPU application can cause memory segfaults when a Garbage Collector (GC) moves or de-allocates buffers while the GPU kernels (or functions running on any other accelerators) are still running.
- This work presents a technique to allocate the Java heap in Unified Memory (a single memory address space between the CPU and the GPUs).
- Accelerators such as GPUs that support Unified Memory will migrate memory pages under demand between the host (CPU) and the device (accelerator) in a transparent manner.
- This post shows the details of this technique and shows the potential for using UM, especially in the context of shared memory computing systems (e.g., integrated GPUs).


## Introduction

Managed runtime programming environments usually include a memory manager for handling allocations, and a Garbage Collector (GC) for managing different heap areas and deallocating objects automatically.  Thus, developers do not need to perform memory management manually. This memory model works well for applications running on CPUs supported by the VM. However, programming heterogeneous hardware from managed runtime programming languages is a more challenging task.


Programming Graphics Processing Units (GPUs) from languages like Java involves copying objects (for example primitive arrays) from the Java heap to the device memory. However, programmers must be cautious that the GC does not collect any objects used when the GPU runs the kernels. Otherwise, a seg-fault may occur when trying to access Java objects once the GPU kernel finishes the execution and tries to copy back results to the CPU. This happens when on-heap objects need to be offloaded (outside the Java heap area), and the JVM cannot know if objects can be moved because the GPU is using them unless they are pinned. 


To give an example, this is [a comment from the JOCL project](https://github.com/gpu/JOCL/blob/master/src/main/java/org/jocl/CL.java#L2043-L2054), a Java wrapper for programming OpenCL kernels for heterogeneous hardware:


  > "Non-blocking operations are not supported for pointers to Java arrays. 
  The Java array may be garbage collected if there no longer exists a
  reference to the array. But even if a reference is retained, the JVM 
  memory management is allowed to move the memory of a Java array, 
  at any time."


This is not a particular issue only for the JOCL project. This issue also exists in [Aparapi](https://github.com/Syncleus/aparapi) (a Java parallel programming framework for offloading Java compute kernels into GPUs), and [TornadoVM](https://www.tornadovm.org/) (a plugin to OpenJDK and a parallel programming framework for accelerating Java workloads on multiple hardware accelerators). At the time of writing this post, these solutions use on-heap data. Thus, to avoid potential problems with the GC, Aparapi and TornadoVM make blocking calls when running on GPUs and FPGAs. 


## But, what if...

The issues with the GC are mainly because the Java memory manager and the Java GC do not have access to the GPU's memory, and the GPU driver does not know if pointers are moved or not by the Java runtime system. But, what if the Java heap is in a space that both the CPU and the GPU can access? In this post, we explore GPU Unified Memory as a Java heap.
 
This post is based on a recent paper we just published at the MPLR conference in 2023, which is also based on the initial work of [Florin Blanaru](https://www.linkedin.com/in/florin-blanaru-03a411153/) during [his Master Thesis](https://pure.manchester.ac.uk/ws/portalfiles/portal/213189790/FULL_TEXT.PDF), in which he initiated the work towards Unified Memory for the MaxineVM. Later, we extended this work and generalized it for other parallel programming models like Intel Level Zero and included support not only for discrete GPUs (e.g., an NVIDIA 3070) but also for integrated GPUs (Intel HD Graphics).  The rest of this post will discuss and summarize the key findings of the paper. 



## GPU Unified Memory

GPU Unified Memory is a single virtual memory address space that is accessible from all processors in a system.  For discrete GPUs, the way it works is that there is a single pointer that is accessible for both the CPU and the GPU. When the data is requested/accessed from the GPU and the corresponding memory pages are not present, the GPU driver will issue a memory page fault. Then the CPU and the GPU will work together to allocate the memory pages on the GPU, migrate the data and deallocate the corresponding memory pages from the CPU. When the CPU requests access to the data again and the memory pages are not present, then the GPU driver and the CPU work together to bring those pages back.


This process is transparent for the programmer, and memory pages are migrated under demand between the CPU and the GPU.  The benefit of having the Java heap allocated in the Unified Memory is that the GC can move objects and trigger memory page migration from the GPU to the CPU while the GPU kernel runs (at the cost of performance), without any segmentation faults. Besides, it simplifies the programming model, since explicit copies are not required. 


In the context of integrated GPUs, which use the same system’s memory, the unified memory process is simplified, because there is no memory page migration between these devices. As a side note, NVIDIA uses the term Unified Memory, while [Intel and oneAPI](https://www.intel.com/content/www/us/en/docs/oneapi/optimization-guide-gpu/2023-1/unified-shared-memory-allocations.html) (and Level Zero) use the term Unified Shared Memory (USM). For the rest of the post, we will refer to Unified Memory for both technologies. 


## Prototype

We prototyped this technique in the context of [MaxineVM](https://maxine-vm.readthedocs.io/en/stable/), a research Virtual Machine initiated by Sun Microsystems (and later Oracle Labs) that is fully implemented in Java, including the Garbage Collector, JIT compilers, class-loader, etc. Besides, the JIT compiler of MaxineVM was taken and rebranded by Oracle Labs to create [Graal](https://www.graalvm.org/).
 
We extended MaxineVM to use Unified Memory for two types of GPUs and parallel programming frameworks: for discrete GPUs using [CUDA Unified Memory](https://developer.nvidia.com/blog/maximizing-unified-memory-performance-cuda/), and discrete GPUs using [Intel Level Zero API](https://dgpu-docs.intel.com/technologies/level-zero.html) (a low-level API part of the oneAPI software ecosystem for parallel programming). 


## So, okay, Unified Memory, what is the deal?


In a nutshell, the idea is very simple: during the bootstrap of the VM, we allocate the Java heap area on Unified Memory. However, there are two main subsequent issues with this approach:


1. The bootstrap process of the VM needs access to accelerator resources, such as driver pointer, device pointer, context pointers and command queues (or streams). These are specific pointers needed for allocating buffers on Unified Memory. These pointers need to be shared also with the host application (e.g., the Java applications that run on top of the VM). Otherwise, the host application will not have access to the same Unified Memory.


2. Since the Java heap is now a shared resource between the CPU and the GPUs, it needs to be accessed/updated with caution to avoid race conditions. Thus, a synchronization mechanism is needed.


To solve these two issues, we introduced a new shared programming interface between the VM and the host applications called `XPUInterface`, and a blocking mechanism during the process pending command from the GPU execution stream before performing the GC.


## XPU Interface


Depending on the parallel programming model used underneath, we need to create and access different pointers to be able to allocate a buffer in Unified Memory. If we use CUDA, we only need the size of the buffer and a pointer. However, if we use other parallel programming models such as OpenCL or Intel Level Zero, we need access to a Level Zero context, which again, is created using the device and driver pointers.  


Furthermore, the VM also needs to perform some synchronization points over the Intel Level Zero command lists and CUDA Streams. Thus, the VM also needs to create and access these pointers. But if the VM creates all these pointers to access and synchronize the Unified Memory, the client applications need to access the same pointers to be able to use the same Unified Memory. Thus, we create a common interface, called XPU Interface in which the VM will store all these pointers. Then, client applications will use these pointers to access the same memory, and perform operations (e.g., launching kernels, adding events, etc).


The following code snippet shows a representation of the XPU Interface. The VM will use this interface to set all values per selected backend (e.g., CUDA and Level Zero). 
 
```java
public interface XPU_Interface {
    long getDriver(int heapIndex);
    long getDevice(int heapIndex);
    long getContext(int heapIndex);
    long getCommandQueue(int heapIndex);
    long getCommandList(int heapIndex);
}
```


Then, the client application can consume those values by querying an implementation of this interface:



```java
XPUImpl xpu = XPUImpl(Runtime.getXPUCUDAInitialization());
 
// Example: get Context Pointer
long contextPtr = xpu.getContextHandler();
```


## Example of Workflow using Level Zero UM.


The following diagram shows the pointer dependencies to build each data structure with the l Level Zero backend for the VM and the client parts. In the case of the Level Zero backend, the client needs to compile a kernel in [SPIR-V](https://www.khronos.org/spir/) format. The SPIR-V code can be obtained, for example with CLANG and the [LLVM SPIR-V toolchain](https://github.com/intel/llvm). 


<p align="center">
<img width="700" height="" src="https://github.com/jjfumero/jjfumero.github.io/blob/master/files/blog/usmOct23/usm-l0-workflow.png?raw=true">
</p>


The SPIR-V is a standard intermediate portable representation for compute and graphics of hardware accelerators. Similarly, if the CUDA backend is selected, developers are required to attach the CUDA C kernels or the CUDA PTX kernels (CUDA assembly code).


After this, the developer can start the VM process for running the application. During the VM bootstrap, the VM will create all data structures needed and pass them along through the XPU Interface implementation. Then, the client can read those pointers and use them in the application. When the kernel is launched, the data needed comes from the Unified Memory that both the CPU and the GPU can understand. 



## Avoiding Race Conditions

To avoid race conditions, we insert a synchronized point before performing the GC. This is because only the GC can move the host pointers while the GPU application still runs. The synchronization points use the CUDA Streams or the Level Zero command queue and command lists data structures. 
 

An important note here is that, if we do not have the sync point, the VM still runs the code with no segmentation faults. This is simply because the page fault mechanism will take place. However, since the Java heap is a shared resource and both CPU and GPU might update the same memory pages before the GC moves an object, it needs to guarantee that the GPU execution is completed. Thus, in a sense, this strategy also needs to block the GC, however, this time, for a different reason. So, is there any benefit? Before discussing the potential benefits, let’s discuss the performance we obtain with this approach. 


## Evaluation (Key Findings)


In the [paper](https://github.com/jjfumero/jjfumero.github.io/blob/master/files/papers/2023/jfumero-mplr2023-unified-memory.pdf), we have an extensive performance evaluation and analysis. In fact, we worked on the evaluation for several months, using different setups and different combinations, to try to understand the behaviour of each case scenario. In this post, I will summarize the key points, so for more in-depth analysis, I will point out the paper. 
What we want to find out is the following:



#### Q1: Is there any performance penalty if the Java Heap is allocated on Unified Memory, but the GPU is not used?


To answer this question, we evaluated our approach using the DaCapo and Renaissance benchmark suites with MaxineVM on CPUs and compared them with non-unified memory (unmodified version). We saw that, in general, there is no significant performance penalty (with relative speedups of 0.98x and 1.01x for CUDA and Level Zero respectively for the DaCapo benchmarks, and 0.98x and 0.9x for the Renaissance benchmark).  

The exception was for one of the benchmarks in the Renaissance suite, called `akka-uct` for the CUDA UM, which had an overhead of 12%. This benchmark is a non-uniform workload and it is more intensive in terms of object allocation. One thing to notice though is, that when using Unified Memory in CUDA, the allocation only reserves the virtual address space, even for CPUs. Thus, when a memory address is requested, it fails because there is still no memory page assigned. Then, it will provoke a page fault, and the runtime system will allocate the corresponding memory pages for the requested addresses. Since the `akka-uct` benchmark is a  non-uniform workload distribution, it will put more pressure on how NVIDIA implements Unified Memory regarding page faulting and page allocation. 


#### Q2: How is the performance GPUs with Unified Memory compared to GPU applications that do not use Unified Memory? 

Without going into much detail, the CUDA implementation with Unified Memory runs ~5x-10x  slowdown compared to a GPU application that does not use Unified Memory. This is because, on discrete GPUs, the Unified Memory page migration is under demand, and this can slow down the data migration process. 

However, there are techniques (we did not include those in this study) to prefetch data, even with Unified Memory. Thus, what we saw in the evaluation, is the worst-case scenario. 
Regarding the Level Zero implementation with the Intel-integrated GPU, we saw that, in general, running with Unified Memory offers higher performance (up to 10x) compared to non-unified memory. This is because, if we do not use Unified Memory with an integrated GPU, we need to perform an extra copy of data (from a host buffer to a device buffer). Thus, in general, it is recommended to use Unified Memory with this setup. 


#### Q3: Is there any slowdown when we run a System.gc()? 


We measured the time that takes a full GC in MaxineVM. Our version of MaxineVM uses a semispace Garbage Collector, so all objects within the heap are moved from one subspace to another. We saw that, when using CUDA Unified Memory, the performance overheads are up to 2.13x slowdown compared to not using Unified Memory. This is because data is migrated from the GPU to the CPU first (because the GC touches the same memory pages).


In the case of the integrated GPU, although we saw some differences (ranging from 0.97x to 1.04x), we do not consider either performance penalty or performance benefit. It runs at the same speed as non-unified memory. This is because, although the GC touches the same memory pages, there is no data transfer (data migration) from the GPU to the CPU. 

For more details, we have more results, and we discuss in more detail each of these experiments in the paper. 


## Analysis and Conclusions

**This work has shown that it is possible to use Unified Memory as a Java heap. But, is it practical?**

On one side, it solves the potential segmentation faults, and it allows us to launch of non-blocking operations on the GPU. Furthermore, due to the VM being aware of the hardware accelerators and the memory being used, the GC can operate without provoking a seg-fault. 


But, on the other side, still, the GC needs to perform a synchronization point before invoking a full GC. In order for this solution to be practical, we propose a Java heap with multiple memory areas, and the memory areas can be heterogeneous. For example, some of those could be using Unified Memory. The issue is to organize objects in such a way that does not affect the fast path for allocating objects (in Java). We must be careful here not to introduce more control flow that slows down decisions about where to locate/promote an object that can be used on the GPU. A possible solution for this could be to tag/annotate those objects. 


I believe this is an interesting work and has shown the potential, the benefits and the drawbacks of this approach. However, despite the fact that I like this work, it adds extra complexity to current and production VMs. An alternative approach is to allow buffer objects to be off-heap. Projects such as the [Panama API](https://github.com/openjdk/panama-foreign) will allow heterogeneous managed runtime systems to do this easily. 

 

## Follow-up and discussions

If you get this far, and you're still interested in this work, you can check the paper, and/or drop me an email for more discussions. You can also leave your comments on [GitHub](https://github.com/jjfumero/jjfumero.github.io/discussions/10), or contact my via [Twitter](https://twitter.com/snatverk). 

