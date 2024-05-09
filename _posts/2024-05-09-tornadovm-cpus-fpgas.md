---
title: 'Running TornadoVM on CPUs and FPGAs via oneAPI'
date: 2024-05-09
permalink: /posts/2024/05/09/tornadovm-cpus-fpgas-oneapi
author_profile: false
tags:
  - TornadoVM
  - Java 
  - CPUs
  - FPGAs
  - Intel oneAPI 
  - Performance
excerpt: "This post shows the main steps to install and run TornadoVM on CPUs and FPGAs using the Intel oneAPI runtime for OpenCL."
---

## Key Takeaways

<img align="right" style="width:200px;" src="https://raw.githubusercontent.com/jjfumero/jjfumero.github.io/master/files/blog/tornadovm-multibackend/yt-04-frontThumbnailPost.jpg">


## Introduction

In previous posts, we have discussed how to run data-parallel applications on GPUs using TornadoVM. But what if you do not have a GPU? Can we run TornadoVM applications on CPUs and take advantage of all cores? 

The answer is yes. All you need is an OpenCL implementation that can run on your CPU. In this post, I will show how you can configure TornadoVM to run on such systems using the Intel oneAPI base toolkit for Intel CPUs. Note that there are also other OpenCL implementations for CPUs, such as   [PoCL](http://portablecl.org), that can run not only on Intel architectures, but also on ARM and even on RISC-V architectures. I may explore this in future technical articles. 


In the case you prefer it, I do have a video-version of this tutorial on YouTube:

[![Watch the video](https://img.youtube.com/vi/lJHSpw97yDE/hqdefault.jpg)](https://www.youtube.com/embed/lJHSpw97yDE)

## Installing Intel oneAPI:

Installing Intel oneAPI is an easy process. All you need is the installer package that you can download from the Intel oneAPI website (https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.htm) . There are different toolkits within the Intel oneAPI, but for running TornadoVM with OpenCL, we only need the base toolkit. If we select the offline installer for Linux, we run the following commands to complete the installation process. 

```bash
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/fdc7a2bc-b7a8-47eb-8876-de6201297144/l_BaseKit_p_2024.1.0.596_offline.sh

sudo sh ./l_BaseKit_p_2024.1.0.596_offline.sh
```

Note that, by default, the Intel oneAPI will be installed in `/opt/intel/oneapi`. It is important to know this directory to set up the environment when running with oneAPI. Once the installation is finished, we are ready to run on the CPU. 


## Running on the CPU with TornadoVM 

If we have configured TornadoVM to use the OpenCL backend, we do not have to recompile TornadoVM. Just run the applications on the CPU device. 

If I run `tornado` with the `--devices` option on my development system, I get the following devices (using TornadoVM 1.0.4):

```bash
$ tornado --devices

Number of Tornado drivers: 1
Driver: OpenCL
  Total number of OpenCL devices  : 2
  Tornado device=0:0  (DEFAULT)
	OPENCL --  [NVIDIA CUDA] -- NVIDIA GeForce RTX 3070
		Global Memory Size: 7.8 GB
		Local Memory Size: 48.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: [1024]
		Max WorkGroup Configuration: [1024, 1024, 64]
		Device OpenCL C version: OpenCL C 1.2

  Tornado device=0:1
	OPENCL --  [Intel(R) OpenCL HD Graphics] -- Intel(R) UHD Graphics 770
		Global Memory Size: 24.9 GB
		Local Memory Size: 64.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: [512]
		Max WorkGroup Configuration: [512, 512, 512]
		Device OpenCL C version: OpenCL C 1.2
```


But, wait, I still do not see the CPU as a target device. In my case, I have access to two devices, an NVIDIA RTX 3070, and an Intel integrated GPU. To get access to the CPU, we need to load the Intel oneAPI environment:

```bash
$ source /opt/intel/oneapi/setvars.sh

:: initializing oneAPI environment ...
   bash: BASH_VERSION = 5.2.26(1)-release
   args: Using "$@" for setvars.sh arguments: 
:: advisor -- latest
:: ccl -- latest
:: compiler -- latest
:: dal -- latest
:: debugger -- latest
:: dev-utilities -- latest
:: dnnl -- latest
:: dpcpp-ct -- latest
:: dpl -- latest
:: ipp -- latest
:: ippcp -- latest
:: mkl -- latest
:: mpi -- latest
:: tbb -- latest
:: vtune -- latest
:: oneAPI environment initialized ::
```

If we inspect the TornadoVM devices again, we see the following:

```bash
tornado --devices
WARNING: Using incubator modules: jdk.incubator.vector

Number of Tornado drivers: 1
Driver: OpenCL
  Total number of OpenCL devices  : 4
  Tornado device=0:0  (DEFAULT)
	OPENCL --  [NVIDIA CUDA] -- NVIDIA GeForce RTX 3070
		Global Memory Size: 7.8 GB
		Local Memory Size: 48.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: [1024]
		Max WorkGroup Configuration: [1024, 1024, 64]
		Device OpenCL C version: OpenCL C 1.2

  Tornado device=0:1
	OPENCL --  [Intel(R) OpenCL HD Graphics] -- Intel(R) UHD Graphics 770
		Global Memory Size: 24.9 GB
		Local Memory Size: 64.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: [512]
		Max WorkGroup Configuration: [512, 512, 512]
		Device OpenCL C version: OpenCL C 1.2

  Tornado device=0:2
	OPENCL --  [Intel(R) OpenCL] -- 12th Gen Intel(R) Core(TM) i7-12700K
		Global Memory Size: 31.1 GB
		Local Memory Size: 32.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: [8192]
		Max WorkGroup Configuration: [8192, 8192, 8192]
		Device OpenCL C version: OpenCL C 3.0

  Tornado device=0:3
	OPENCL --  [Intel(R) FPGA Emulation Platform for OpenCL(TM)] -- Intel(R) FPGA Emulation Device
		Global Memory Size: 31.1 GB
		Local Memory Size: 256.0 KB
		Workgroup Dimensions: 3
		Total Number of Block Threads: [67108864]
		Max WorkGroup Configuration: [67108864, 67108864, 67108864]
		Device OpenCL C version: OpenCL C 1.2
```

So, based on my configuration, If I want to run a TornadoVM program on the CPU, I need to select device 0:2. And if I want the FPGA, I select device 0:3. Let’s do that. 

I will demonstrate the CPU access with TornadoVM by running the Blur Filter example from [this GitHub repo](https://github.com/jjfumero/tornadovm-examples).

```bash
git clone https://github.com/jjfumero/tornadovm-examples
cd tornadovm-examples
source /path-to-your-Tornado-DIR/source.sh
export TORNADO_SDK=/path-to-your-Tornado-DIR/bin/sdk
mvn clean package
```

And we are ready to run. Notice that, based on the devices I have installed on my system, the CPU OpenCL appears in index 0:2 from the TornadoVM device list. This is the index we need to use to run on the multi-core:

```
tornado --threadInfo -cp target/tornadovm-examples-1.0-SNAPSHOT.jar io.github.jjfumero.BlurFilter tornado device=0:2

WARNING: Using incubator modules: jdk.incubator.vector
Task info: blur.red
	Backend           : OPENCL
	Device            : 12th Gen Intel(R) Core(TM) i7-12700K CL_DEVICE_TYPE_CPU (available)
	Dims              : 2
	Global work offset: [0, 0]
	Global work size  : [20, 1]
	Local  work size  : null
	Number of workgroups  : [0, 0]

Task info: blur.green
	Backend           : OPENCL
	Device            : 12th Gen Intel(R) Core(TM) i7-12700K CL_DEVICE_TYPE_CPU (available)
	Dims              : 2
	Global work offset: [0, 0]
	Global work size  : [20, 1]
	Local  work size  : null
	Number of workgroups  : [0, 0]

Task info: blur.blue
	Backend           : OPENCL
	Device            : 12th Gen Intel(R) Core(TM) i7-12700K CL_DEVICE_TYPE_CPU (available)
	Dims              : 2
	Global work offset: [0, 0]
	Global work size  : [20, 1]
	Local  work size  : null
	Number of workgroups  : [0, 0]

TornadoVM Total Time (ns) = 5526195869 -- seconds = 5.526195869
```


That’s awesome! Now, we can run some benchmarks to see a performance comparison versus sequential and parallel streams. If you want to benchmark on the CPU with TornadoVM, just make sure the right device is selected from the source code:

```diff
diff --git a/src/main/java/io/github/jjfumero/BlurFilter.java b/src/main/java/io/github/jjfumero/BlurFilter.java
index ff4cd42..ffc1cb1 100644
--- a/src/main/java/io/github/jjfumero/BlurFilter.java
+++ b/src/main/java/io/github/jjfumero/BlurFilter.java
@@ -381,7 +381,7 @@ public class BlurFilter {
         @Setup(Level.Trial)
         public void doSetup() {
             // Select here the device to run (backendIndex, deviceIndex)
-            blurFilter = new BlurFilter(Options.Implementation.TORNADO_LOOP, 0, 3);
+            blurFilter = new BlurFilter(Options.Implementation.TORNADO_LOOP, 0, 2);
         }
 
         @Benchmark
```

Then we recompile and run:

```bash
mvn clean package
$ tornado --threadInfo  -cp target/tornadovm-examples-1.0-SNAPSHOT.jar io.github.jjfumero.BlurFilter jmh 

Benchmark                               Mode  Cnt            Score             Error  Units
BlurFilter.Benchmarking.jvmJavaStreams  avgt    5   5966823136.333 ±    22489007.669  ns/op
BlurFilter.Benchmarking.jvmSequential   avgt    5  72104124393.000 ± 15762868825.115  ns/op
BlurFilter.Benchmarking.runTornadoVM    avgt    5   5095359714.433 ±   102896875.200  ns/op
```
If we talk about performance, we also need to talk about the setup. This benchmark was executed with TornadoVM 1.0.4 on an Intel 12th Gen CPU i7-12700K with 20 threads. This CPU has 8 performance cores and 4 efficiency cores. In this type of architecture, the performance cores can run with HT (Hyperthreading), thus, giving us a total of 20 threads to run. Besides, I am using the Intel OpenCL runtime `2024.17.3.0.08_160000` from Intel oneAPI. The Java version is Java HotSpot(TM) 64-Bit Server VM (build 21.0.3+7-LTS-152).

As we see, Java Parallel Streams and TornadoVM perform 12x and 14.1x respectively compared to Java sequential code. We compare it with Java sequential because the input Java code annotated with the `@Parallel` Tornado annotation is the sequential implementation. If we compare TornadoVM versus the Java parallel streams, TornadoVM outperforms the Java parallel streams by 17% running on the same hardware. This is great! 

Now, are you up for a challenge? Is it possible to even run faster? If so, how? Let me know, and, if you like this topic, stay tuned for new performance improvements. Let’s now jump at how we can run on FPGAs using the emulation mode of Intel oneAPI. 


## Running on the FPGA 

Running on FPGAs is a bit tricky compared to the handling of the execution on GPUs and CPUs. This is because, to compile for FPGAs, we need a new compiler and it usually takes a long time (> 30-40 mins). 

Fortunately, some FPGA vendors, like Intel, give us the option to emulate the compilation and execution on an FPGA, even if we don’t have a physical FPGA, which is my case. 

To run TornadoVM with FPGA emulation mode, we need to set up a new env variable, and then run TornadoVM as usual. This env variable is tailored to Intel FPGAs, so, bear in mind that, if you use an FPGA from another vendor, this env variable might change. 

To demonstrate the FPGA workflow, I will select another example from the `tornadovm-example` suite. Ideally, we should be able to run the same application (Blur Filter). However, the FPGA in TornadadoVM is still in active development where this application produces some compilation errors. But we can still run on the FPGA using another example:

```bash
$ env CL_CONTEXT_EMULATOR_DEVICE_INTELFPGA=1 tornado --threadInfo --jvm="-Ds0.t0.device=0:3" -cp target/tornadovm-examples-1.0-SNAPSHOT.jar io.github.jjfumero.Mandelbrot tornado

Task info: s0.t0
	Backend           : OPENCL
	Device            : Intel(R) FPGA Emulation Device CL_DEVICE_TYPE_ACCELERATOR (available)
	Dims              : 2
	Global work offset: [0, 0]
	Global work size  : [512, 512]
	Local  work size  : [64, 1, 1]
	Number of workgroups  : [8, 512]

Total Time (ns) = 495149249 -- seconds = 0.495149249
```

We just run the Mandelbrot program on the FPGA in emulation mode! As a disclaimer, never measure performance in emulation mode. This is just for debugging as it never runs on the real hardware, and it is in fact, emulated on the CPU. 



If you want to deep-dive into the FPGA compilation, the TornadoVM JIT compiler generates a new folder called `fpga-source-comp` (which we can also tune) with all the FPGA generated binaries (called bitstreams) as well as the log information. If we inspect the file `fpga-source-comp/outputFPGA.log`, we see the following content:

```bash
Command: [aocl-ioc64, --input=/home/juan/repos/tornadovm-examples/fpga-source-comp/mandelbrotFractal.cl, --device=fpga_fast_emu, --cmd=build, --ir=/home/juan/repos/tornadovm-examples/fpga-source-comp/mandelbrotFractal.aocx]

Setting target instruction set architecture to: Default (Intel(R) Advanced Vector Extensions 2 (Intel(R) AVX2))
Platform name: Intel(R) FPGA Emulation Platform for OpenCL(TM)
Device name: Intel(R) FPGA Emulation Device
Device version: OpenCL 1.2 
Device vendor: Intel(R) Corporation
Device profile: EMBEDDED_PROFILE
Using build options:  -I "/home/juan/repos/tornadovm-examples/fpga-source-comp"
Compilation started
Compilation done
Linking started
Linking done
Device build started
Options used by backend compiler: 
Device build done
Kernel "mandelbrotFractal" was successfully vectorized (8)
Done.
--------------------------------------------------------------------
Standard output:
Command: [/home/juan/tornadovm/TornadoVM/bin/sdk/bin/cleanFpga.sh, intel, /home/juan/repos/tornadovm-examples/fpga-source-comp/, mandelbrotFractal]
```

This means that TornadoVM, after the JIT compiler transformed the Java bytecode to OpenCL C code, compiled the final binary with the `aocl-ioc64` compiler tool. This can be tuned. What happens is that TornadoVM knows how to compile for Intel and Xilinx FPGAs, and it uses a configuration file. By default, the configuration file is in `$TORNADO_SDK/bin/sdk/etc/intel-oneapi-fpga.conf`

```bash
cat $TORNADO_SDK/bin/sdk/etc/intel-oneapi-fpga.conf

# Configure the fields for FPGA compilation & execution
# [device]
DEVICE_NAME = fpga_fast_emu
# [compiler]
COMPILER = aocl-ioc64
# [options]
DIRECTORY_BITSTREAM = fpga-source-comp/ # Specify the directory
```

We can tune these values, or, even better, we can provide our own configuration files. To pass a new configuration file for the FPGA, we use the `-Dtornado.fpga.conf.file=FILE`

This process is a bit cumbersome, but keep in mind that TornadoVM is still an academic project. Hopefully, the workflow will improve in future versions. 

## Conclusions

TornadoVM can run on any compatible hardware accelerator with the backend implementations (OpenCL, PTX and SPIR-V if we take the latest version at the time of writing this post, 1.0.4). The hardware acceleration selection includes, not just GPUs, but also CPUs and FPGAs. This post has shown how to run and benchmark Java programs accelerated with TornadoVM to run on multi-cores CPUs through its OpenCL backend and the Intel oneAPI implementation. In addition, since the Intel oneAPI also provides an FPGA emulation mode, this post has shown how to access and configure TornadoVM to run in such devices. 


- [Link for Discussions](https://github.com/jjfumero/jjfumero.github.io/discussions/12)

