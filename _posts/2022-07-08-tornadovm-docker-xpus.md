---
title: 'Running Java Programs on XPUs with TornadoVM via Docker'
date: 2022-07-08
permalink: /posts/2022/07/tornadovm-docker-xpus
author_profile: false
tags:
  - TornadoVM
  - Docker
  - GPUs 
  - CPUs
  - FPGAs
excerpt: "In this post, we will show how to launch and accelerate Java programs on heterogeneous hardware via TornadoVM with minimal configuration using pre-built Docker images"
---

## Key takeaways:
- Installing drivers, compilers and configuring heterogeneous hardware can be a tedious process (especially for FPGAs). 
- Field Programmable Gate Arrays (FPGAs) are usually seen as hard-to-run hardware accelerators, requiring complex installation and need for handling of a license server. 
- Intel oneAPI provides easy access to Intel GPUs, CPUs and FPGAs through their Docker images.
- TornadoVM can be used with a Docker image that extends the Intel oneAPI one, to enable Java programs to access Intel Integrated GPUs, multi-core CPUs and FPGAs.
- This post explains, with an example, how to run Java applications using the TornadoVM APIs on Intel accelerators. 


## Intro

The more hardware we have available, the more drivers, software stacks, configuration files, etc., we need to deal with. This is especially cumbersome when installing and configuring FPGAs. Fortunately, solutions such as Docker containers can provide a much higher level of abstraction to all of these configurations. 

In this post, we will show how to launch and accelerate Java programs on heterogeneous hardware via TornadoVM with minimal configuration using pre-built Docker images. Thus, trying and running TornadoVM has never been easier! Excited? Let's run a few examples. 


*Assumptions to follow this post:*

- We assume you are familiar with how to program with [TornadoVM](https://github.com/beehive-lab/TornadoVM) and how [Task graphs are composed](https://www.infoq.com/articles/tornadovm-java-gpu-fpga/) in order to accelerate Java methods on heterogeneous hardware.
- We assume you have Intel hardware accessible in order to try the pre-built TornadoVM Docker images. 


## How to run? 

The Docker images of TornadoVM include the whole TornadoVM SDK, examples and benchmarks. Additionally, they enable the execution of user applications by invoking the running script (e.g., `run_intel_openjdk.sh`) provided on the GitHub repository.

```bash
git clone https://github.com/beehive-lab/docker-tornado
cd docker-tornado 
```


To obtain the list of accelerators accessible from the Docker image, we can execute the following TornadoVM command (note that the first time this command is executed, it will pull the Docker image from DockerHub, so you will need an internet connection):

```bash
$ ./run_intel_openjdk.sh tornado --devices

Number of Tornado drivers: 2
Driver: SPIRV
  Total number of SPIRV devices  : 1
  Tornado device=0:0
	SPIRV -- SPIRV LevelZero - Intel(R) UHD Graphics [0x9bc4]
		Global Memory Size: 24.9 GB
		Local Memory Size: 64.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: 256
		Max WorkGroup Configuration: [256, 256, 256]
		Device OpenCL C version:  (LEVEL ZERO) 1.3

Driver: OpenCL
  Total number of OpenCL devices  : 3
  Tornado device=1:0
	OPENCL --  [Intel(R) FPGA Emulation Platform for OpenCL(TM)] -- Intel(R) FPGA Emulation Device
		Global Memory Size: 31.1 GB
		Local Memory Size: 256.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: 67108864
		Max WorkGroup Configuration: [67108864, 67108864, 67108864]
		Device OpenCL C version: OpenCL C 1.2

  Tornado device=1:1
	OPENCL --  [Intel(R) OpenCL] -- Intel(R) Core(TM) i9-10885H CPU @ 2.40GHz
		Global Memory Size: 31.1 GB
		Local Memory Size: 32.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: 8192
		Max WorkGroup Configuration: [8192, 8192, 8192]
		Device OpenCL C version: OpenCL C 3.0

  Tornado device=1:2
	OPENCL --  [Intel(R) OpenCL HD Graphics] -- Intel(R) UHD Graphics [0x9bc4]
		Global Memory Size: 24.9 GB
		Local Memory Size: 64.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: 256
		Max WorkGroup Configuration: [256, 256, 256]
		Device OpenCL C version: OpenCL C 1.2
```

We can see that TornadoVM has access to two backends: 1) SPIR-V via the Level Zero dispatcher, and 2) OpenCL. When running with the SPIR-V backend, TornadoVM will use the Intel Integrated Graphics (Intel(R) UHD Graphics) that is available on the host machine. This is because the dispatching of the generated SPIR-V kernel is done through Intel Level Zero, which is currently only available for Intel GPUs. When using the OpenCL backend, TornadoVM can run on 3 different devices: a) an Intel FPGA in [emulation mode](https://github.com/beehive-lab/TornadoVM/blob/master/assembly/src/docs/7_FPGA.md#3-emulation-mode); b) an Intel Multi-core CPU (same CPU as the host machine); and c) an Intel Integrated Graphics. Note that the integrated GPU is the same device as shown through the SPIR-V backend, but it is accessible from a different driver (OpenCL instead of Level Zero). 

Now that we know which accelerators are accessible from the Docker image, we can launch our Java/TornadoVM applications on that hardware. 

Let's run an example provided in the same repository:


```bash
cd example

## Compile the application with Maven
$../run_intel_openjdk.sh  mvn clean package

## Launch the Java application on the default accelerator 
# (device 0:0, SPIR-V on the Intel HD Graphics)
$../run_intel_openjdk.sh tornado --threadInfo -cp target/example-1.0-SNAPSHOT.jar example.MatrixMultiplication 256
```

This last command will execute the Matrix Multiplication example on the default device, the Intel HD graphics using the SPIR-V Backend. 

We can obtain the generated disassembled SPIR-V by running with the option `--printKernel`:


```bash
$../run_intel_openjdk.sh tornado --printKernel --threadInfo -cp target/example-1.0-SNAPSHOT.jar example.MatrixMultiplication 256

; MagicNumber: 0x7230203
; Version: 1.2
; Generator ID: 32
; Bound: 137
; Schema: 0

                                   OpCapability Addresses
                                   OpCapability Linkage
                                   OpCapability Kernel
                                   OpCapability Int64
                                   OpCapability Int8
                              %1 = OpExtInstImport "OpenCL.std"
                                   OpMemoryModel Physical64 OpenCL
                                   OpEntryPoint Kernel %41 "matrixMultiplication" %spirv_BuiltInGlobalInvocationId %spirv_BuiltInGlobalSize 
                                   OpSource OpenCL_C 300000  
                                   OpName %spirv_BuiltInGlobalInvocationId "spirv_BuiltInGlobalInvocationId"
                                   OpName %spirv_BuiltInGlobalSize "spirv_BuiltInGlobalSize"
                                   OpName %__kernelContextF0 "__kernelContextF0"
                                   OpName %__kernelContextF0_ptr "__kernelContextF0_ptr"

...
```

We will see the whole SPIR-V code during the first iteration of the application. 

## Running on the FPGA (Emulation Mode) 

We can change the device in which the TornadoVM will launch the application. To do so, we can use a command-line flag to specify the backend and the device index to use. The Docker image is configured to have the FPGA device indexed with `backend:index` set to `1:0`. To change the device, we use the flag `-D<taskScheduleName>:<taskName>.device=1:0`. So now, what we need is the name of the `TaskSchedule` and the Task used in the TornadoVM program. We can obtain the names from the [source code](https://github.com/beehive-lab/docker-tornado/blob/master/example/src/main/java/example/MatrixMultiplication.java#L66-L69). We see that the programmer used the "s0" Java string to identify the `TaskSchedule` and the string "t0" to identify the Task. Therefore, we can change the device and run on the FPGA (in emulation mode) using the following command:



```bash
$ ../run_intel_openjdk.sh tornado -Ds0.t0.device=1:0 --printKernel --threadInfo -cp target/example-1.0-SNAPSHOT.jar example.MatrixMultiplication 256

Task info: s0.t0
	Backend           : OPENCL
	Device            : Intel(R) FPGA Emulation Device CL_DEVICE_TYPE_ACCELERATOR (available)
	Dims              : 2
	Global work offset: [0, 0]
	Global work size  : [256, 256]
	Local  work size  : [64, 1, 1]
	Number of workgroups  : [4, 256]
```

So what we have done is to run exactly the same Java program we launched for the GPU, but targetting an FPGA, just by changing from the command line the device to select during execution. One of the things to notice is that the FPGA execution is in emulation mode. But what does this mean? Let's take a look.


## Running on FPGAs in emulation mode
 
Compiling OpenCL, SPIR-V or PTX kernels for GPUs is relatively a fast process (usually ~200-500ms), depending on the Kernel. However, compiling for FPGAs usually takes hours (1-6h). This is due to the physical routing and mapping of accelerated kernels into hardware. Tools and compilers get better and faster every day, however, still, this is a very slow process. 


To circumvent this problem, FPGA vendors usually provide an emulation and debug environment in order to check the kernels without waiting hours for the final bitstream FPGA binary. 

Intel oneAPI provides tooling for fast access to FPGAs without the need to have a physical FPGA attached to the system. This is very handy, especially for debugging. When running in the FPGA emulation mode, the code will be executed on the host CPU. Therefore, do not measure performance when running in this mode. For more documentation about how Intel FPGAs work, please check the [Intel oneAPI documentation](https://www.intel.com/content/www/us/en/develop/documentation/oneapi-programming-guide/top/programming-interface/fpga-flow/types-of-dpcpp-fpga-compilation.html#types-of-sycl-fpga-compilation_fpga-emulator).


## Using TornadoVM to run vectorized code on CPUs. 

TornadoVM can also run on multi-core CPUs using the OpenCL backend. In this case, the OpenCL driver can parallelize an application using all cores available as well as the vector units present on the CPUs (e.g., SSE, AVX, AVX2, etc). 


```bash
../run_intel_openjdk.sh tornado -Ds0.t0.device=1:1 --printKernel --threadInfo -cp target/example-1.0-SNAPSHOT.jar example.MatrixMultiplication 256

Task info: s0.t0
	Backend           : OPENCL
	Device            : Intel(R) Core(TM) i9-10885H CPU @ 2.40GHz CL_DEVICE_TYPE_CPU (available)
	Dims              : 2
	Global work offset: [0, 0]
	Global work size  : [16, 1]
	Local  work size  : null
```

The CPU used is the same as the host CPU. 


## Conclusions

Having multiple hardware accelerators can hinder the maintenance and the configuration of the whole computing system due to the installation and configuration of different drivers and software stacks, especially when it comes to FPGAs. One of the easiest ways to try new software is via pre-built configured software on containers, such as Docker. 

In this post, we showed how to execute Java programs on a wide diversity of heterogeneous hardware from a single Docker image. 


## Acks
Thanks to [Christos Kotselidis](https://www.kotselidis.net/) and [Thanos Stratikopoulos](https://stratika.github.io/) from the University of Manchester for their constructive feedback and comments. 
I want to thank **Steve Dohrmann** from Intel for his support and guidelines to configure TornadoVM to run on Intel Docker oneAPI images. 

________________________________

<a href="https://www.buymeacoffee.com/snatverk" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" style="height: 60px !important;width: 217px !important;" ></a>

