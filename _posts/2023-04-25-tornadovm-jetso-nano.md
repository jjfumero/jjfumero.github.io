---
title: 'Running TornadoVM on NVIDIA Jetson Nano '
date: 2023-04-25
permalink: /posts/2023/04/tornadovm-jetson-nano
author_profile: false
tags:
  - TornadoVM
  - NVIDIA Jetson Nano
  - Machine Learning 
  - Deep Learning
excerpt: "In this post, we will show how TornadoVM can be used on an NVIDIA Jetson Nano."
---

## Introduction

Did you know that TornadoVM can also run on ARM-based systems with NVIDIA GPUs? In this post, we will show how TornadoVM can be used on an [NVIDIA Jetson Nano](https://www.nvidia.com/es-es/autonomous-machines/embedded-systems/jetson-nano/), a small, powerful computer designed for embedded artificial intelligence (AI) and machine learning (ML) applications. 


<img align="right" width="230" height="" src="https://github.com/jjfumero/jjfumero.github.io/raw/master/files/blog/jetsonNano/P4250118_DxO.jpg">


The Jetson Nano board features a quad-core ARM Cortex-A57 CPU, and a NVIDIA Tegra X1 128-core Maxwell GPU, as well as 4GB of LPDDR4 memory and support for high-speed connectivity and storage. This is more powerful than my first CUDA GPU, an NVIDIA GT 220 back in 2010!  

NVIDIA also recently announced an updated version, called [Orin Nano](https://developer.nvidia.com/blog/develop-ai-powered-robots-smart-vision-systems-and-more-with-nvidia-jetson-orin-nano-developer-kit/ ), which features an Ampere GPU with 1024 CUDA cores and 32 Tensor cores. However, for this post, we will stick with the previous version, which is the one I have. With this powerful hardware in such a small factor, and support for popular AI and ML frameworks such as TensorFlow and PyTorch, the Jetson Nano is an excellent choice for developing and deploying advanced AI applications in areas such as robotics, autonomous vehicles, and edge computing. 

NVIDIA Jetson Nano can also run Java applications. But how do we run from Java on this NVIDIA Tegra X1 GPU? In this post, we will show how to use TornadoVM to achieve transparent acceleration. Since this platform is mainly designed for AI, we will showcase Java programs used in AI and Machine Learning, such as matrix multiplication and classification clustering. Are you curious? Let us get into this then.  

The NVIDIA Jetson Nano comes with a Linux Ubuntu-based OS for ARM systems, that is included in the  Jetson Nano Developer Kit SD Card Image. NVIDIA provides instructions about how to configure the Jetson Nano and start running [CUDA programs on it](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit). For the rest of the post, we assume that the OS and CUDA are already installed. 

## 1. Installing TornadoVM on NVIDIA Jetson Nano 
 
TornadoVM comes with a script that facilitates the installation and configuration. The script will download the `cmake`, `maven`, and Java dependencies for us. We only need to specify which Java JDK and GPU backend to use. For the Jetson Nano, which is an ARM system, we install TornadoVM with Graal-JDK17 and the PTX backend as follows: 


```bash 
$ git clone https://github.com/beehive-lab/TornadoVM.git  
$ cd TornadoVM  
$ ./scripts/tornadovm-installer --jdk graalvm-jdk-17 --backend ptx 
``` 

As far as I know, there is no OpenCL driver available to run on the NVIDIA Tegra X1. However, TornadoVM can access the GPU through its PTX (NVIDIA CUDA) backend. At the end of the installation process, we will get something like this: 

```bash 
########################################################## 
Tornado build success 
Updating PATH and TORNADO_SDK to tornado-sdk-0.15.1-dev-8fe1d4e 
Binaries: /home/juan/tornado/tornado/bin 
Commit  : 8fe1d4e9b 
########################################################## 
 ------------------------------------------ 
        TornadoVM installation done 
 ------------------------------------------ 
Creating source file .................  [ok] 
To run TornadoVM, first run `. source.sh` 
``` 

Then, we execute the source file: 

```bash 
source sources.sh 
``` 

Finally, we check that TornadoVM can run by displaying the default device: 

```bash 
$ tornado --devices 

Number of Tornado drivers: 1 
Driver: PTX 
  Total number of PTX devices  : 1 
  Tornado device=0:0  (DEFAULT) 
PTX -- PTX -- NVIDIA Tegra X1 
Global Memory Size: 3.9 GB 
Local Memory Size: 48.0 KB 
Workgroup Dimensions: 3 
Total Number of Block Threads: [2147483647, 65535, 65535] 
Max WorkGroup Configuration: [1024, 1024, 64] 
Device OpenCL C version: N/A 
``` 
 
## 2. Let’s run Java on the Jetson Nano GPU with TornadoVM!  

We are going to run two applications: matrix multiplication (which is widely used in Machine and Deep Learning), and KMeans, a clustering classification algorithm used in Machine Learning.


### 2.1 Running Matrix Multiplication: 
 
The algorithm for matrix multiplication is a fundamental concept in linear algebra and is used in many areas of mathematics, science, engineering, and Machine Learning too! TornadoVM SDK contains plenty of examples, and it includes the matrix multiplication as well. Thus, let’s run it and see the performance we get on the Jetson Nano: 

```bash 

$ tornado -m tornado.examples/uk.ac.manchester.tornado.examples.compute.MatrixMultiplication2D 

Computing MxM of 512x512 
        Single Threaded CPU Execution: 0.33 GFlops, Total time = 813 ms 
        TornadoVM Execution on GPU (Accelerated): 7.67 GFlops, Total Time = 35 ms 
        Speedup: 23.228571428571428x 
        Verification true 
``` 

As we can see, TornadoVM achieves 23x compared to the Java sequential implementation, and over 7.5 GFLOPS. This includes the data transfers (from the host to the device and vice-versa). Let's now run the KMeans application. 

### 2.2 Running KMeans 

KMeans is a popular unsupervised machine learning algorithm that is used for clustering and grouping data points into different clusters. It is widely used in various applications such as image segmentation, anomaly detection, customer segmentation, and market analysis. In this post we are not going to explain how KMeans is implemented for TornadoVM (maybe in another post), but we are going to run it directly and get a sense of the performance we can get on this platform for this algorithm.  

The KMeans application can be found on the following GitHub repository: 

```bash 
git clone https://github.com/jjfumero/tornadovm-examples 
cd tornadovm-examples 
source /path-to-your-Tornado-DIR/source.sh 
export TORNADO_SDK=/path-to-your-Tornado-DIR/bin/sdk 
mvn clean package 
``` 

Let’s run the KMeans for 1048576 data points (2^20) and three clusters (or three groups). First, we are going to run the sequential implementation: 

```bash 
tornado -cp target/tornadovm-examples-1.0-SNAPSHOT.jar io.github.jjfumero.KMeans seq 1048576 3 
Total time: 10903223898 (ms) 
``` 

As we can see, it took a bit over 10 seconds to compute the clusters. Let’s run now the parallel implementation. Note that the implementation that the example provides, accelerates the assign phase of the KMeans algorithm, in which the distance to the new centroids array is computed. The rest of the phases of the KMeans are still processed on the CPU (without acceleration).  

```bash 
tornado -cp target/tornadovm-examples-1.0-SNAPSHOT.jar io.github.jjfumero.KMeans tornado 1048576 3 
Total time: 2741374225 (ms) 
``` 

The TornadoVM version took 2.7 seconds to compute. This means an acceleration of almost 4x (3.98x) compared to the sequential implementation. Bear in mind that the algorithm randomly chooses centroids for the first iteration, so depending on the centroids that are first selected, we might need to iterate more times. However, if we run the application multiple times, we see that the execution time does not differ that much. Thus, we can get a sense of the performance that we can get on the Jetson Nano for this application and datasets.  

## 3. Recommendations for Heavy Compute Workloads on the NVIDIA Jetson Nano 

<img align="right" width="140" height="" src="https://github.com/jjfumero/jjfumero.github.io/raw/master/files/blog/jetsonNano//P4250099_DxO.jpg">

NVIDIA recommends a power supply that can deliver 5v at 2A via the micro-USB connector. However, the default Jetson nano power consumption is 10w and 2A, but this is only for the GPU module, not with the peripherals. Thus, if we do this, it is quite likely that we will experience black screens, and the GPU does not run at its full potential. Instead, we need to set the Jetson mode to 5watts, but we will lose performance.  

In my case, I have the Jetson nano as a server, so it stays plugged in the whole time. Thus, I can use an external power supply of 5v-4A to the power connector of the Jetson nano. But, if we plugin an external power supply instead of using the micro-USB connector, we must use a jumper in ping J48 to specify that the power should be taken from the dedicated power supply instead. For instructions, you can follow the [excellent guidelines here](https://www.youtube.com/watch?v=jq1OqBe267A).  


## 4. Follow-up and discussions

For further discussions, feel free to contact us, and/or add your comments and feedback on [GitHub](https://github.com/jjfumero/jjfumero.github.io/discussions/9).

_________________________________

If you like the content and it has been useful to you, consider buying me a coffee ;-) 

<a href="https://www.buymeacoffee.com/snatverk" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" style="height: 60px !important;width: 217px !important;" ></a>
