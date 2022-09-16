---
title: 'Exploring Level Zero resources: repositories and purpose'
date: 2022-09-16
permalink: /posts/2022/09/exploring-level-zero-resources/
author_profile: false
tags:
  - Level Zero
  - oneAPI 
  - SPEC
  - Resources
excerpt: "Sometimes, it is not clear which Level Zero repository is the right one for our needs. In this post, we will explain each Level Zero public resource and what they are intended to be."
---

## Intro

[Level Zero](https://dgpu-docs.intel.com/technologies/level-zero.html) is a new bare-metal parallel programming framework proposed by Intel for heterogeneous hardware and accelerators. But where can we find all the information? If we look online, there are a few places in which Level Zero discussions and implementation happen. But sometimes, it is not clear which repository or source is the right one for our needs. In this post, we will explain each Level Zero resource and what they are intended to be. So, if you want to learn, contribute, or participate, this should give you a clearer idea about the organisation of different repositories related to Level Zero and their content. 


## Where is the Level Zero Specification (SPEC)? 

* The Level Zero SPEC: [https://spec.oneapi.io/level-zero/latest/index.html](https://spec.oneapi.io/level-zero/latest/index.html).
* For discussions about the SPEC: [https://github.com/oneapi-src/level-zero-spec](https://github.com/oneapi-src/level-zero-spec)

Developers are welcome to contribute to the SPEC. Here are high-level guidelines about how the documentation is built: 

[https://github.com/oneapi-src/oneAPI-tab/blob/main/tab-level-zero/presentations/22ww24_LevelZeroSpec_TAB.pdf](https://github.com/oneapi-src/oneAPI-tab/blob/main/tab-level-zero/presentations/22ww24_LevelZeroSpec_TAB.pdf)

Note that, as with any other SPEC document, the Level Zero SPEC is usually ahead of the implementation. Therefore, some information in the SPEC might not be implemented by any vendor yet. 

**Do you want to track changes with the SPEC?**

[https://spec.oneapi.io/releases/index.html#level-zero](https://spec.oneapi.io/releases/index.html#level-zero)


## Level Zero Headers, Loader and Validation Layer


[https://github.com/oneapi-src/level-zero](https://github.com/oneapi-src/level-zero)

This is basically the source repository that contains the Level Zero headers that are generated from the SPEC, the loader and the validation layer. This repository, in fact, should have been called something like level-zero-loader.

In my opinion, this is a great place to start working with examples and studying Level Zero. This project can be easily built from source and, as soon as there is a driver implementation (e.g. Intel compute-runtime), all examples can be executed. 


## Level Zero Tests (Conformance and Performance) 
 

[https://github.com/oneapi-src/level-zero-tests](https://github.com/oneapi-src/level-zero-tests)



## Level Zero Driver Implementation for oneAPI.  

[https://github.com/intel/compute-runtime](https://github.com/intel/compute-runtime/)

This repository contains the driver for Intel GPUs with the Intel OpenCL implementation, SPIR-V and Level Zero. Therefore, any issues and questions regarding the Intel implementation of Level Zero for Intel GPUs can be filed in this repository. 




## Level Zero Technical Advisory Board - Meeting Notes


There is a technical advisory board to give and get feedback regarding the next versions of Level Zero. This will help to accommodate different needs for more types of applications and programming languages in future releases. All information discussed in the advisory board is public and available on GitHub: 

[https://github.com/oneapi-src/oneAPI-tab/tree/main/tab-level-zero](https://github.com/oneapi-src/oneAPI-tab/tree/main/tab-level-zero).

This repository contains the material and discussions used during the sessions. In my opinion, this repository is not meant to submit any PR, but rather to follow and get notified of all topics and undergoing work regarding Level Zero.

