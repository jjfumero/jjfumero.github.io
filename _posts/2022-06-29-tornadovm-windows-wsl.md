---
title: 'Running TornadoVM on Intel GPUs using Windows Subsystem for Linux (WSL) for Windows 11'
date: 2022-06-29
permalink: /posts/2022/06/tornadov-intelgpus-windows11-wsl
author_profile: false
tags:
  - TornadoVM
  - Windows 11
  - OpenCL 
  - Level Zero
  - Windows WSL
  - WSL
excerpt: "In this post, I will show you how we can enable TornadoVM to run on Intel HD Graphics via the OpenCL and SPIR-V Backends within WSL using Windows 11"
---

## Intro

Windows Subsystem Linux ([WSL](https://docs.microsoft.com/en-us/windows/wsl)) is a powerful tool to bring native Linux applications to Windows 10 and 11. What is even more appealing though is that we can also execute native applications on GPUs (e.g., [NVIDIA GPUs and Intel Integrated Graphics](https://docs.microsoft.com/en-us/windows/ai/directml/gpu-tensorflow-wsl#intel)).


In this post, I will show you how we can enable TornadoVM to run on Intel HD Graphics via the OpenCL and SPIR-V Backends within WSL using Windows 11.

However, note that, although we focus on TornadoVM, the installation process and configuration for Intel HD Graphics within WSL will allow you to run any console OpenCL/SPIR-V application on the GPU. 


## Enabling Intel Integrated GPUs on Windows 11

If you have an Intel Integrated GPU (iGPU) on your system, you might have the Intel GPU driver already installed by default. However, some systems disable the integrated GPU from the BIOS. So, the first thing to do is to check if the GPU is enabled.


You can obtain this information by pressing right-click over the Windows icon >> Device Manager. Then we unfold the tab “Display Adapters”. We should see the Intel Integrated graphics in this panel. Otherwise, check your BIOS for enabling the Integrated GPU. As a side note, on DELL platforms, the Intel Integrated Graphics is activated in Power Settings >> Advanced >> Intel Multi-Display >> Enable.


<p align="center">
<img width="700" height="" src="https://raw.githubusercontent.com/jjfumero/jjfumero.github.io/master/images/blogs/wsl-tornadovm/DriverInfo.png">
</p>

## Installing the Intel GPU Driver under WSL (Ubuntu 22.04)

Once we have the integrated GPU visible, we need to install the GPU driver within our console in WSL. I am assuming the Linux distribution used is the default when installing WSL, which is Ubuntu. To install the latest Intel GPU drivers, you can check the [Intel Compute Runtime GitHub page](https://github.com/intel/compute-runtime/releases/tag/22.25.23529):


```bash
$ mkdir neo
$ cd neo
$ wget https://github.com/intel/intel-graphics-compiler/releases/download/igc-1.0.11378/intel-igc-core_1.0.11378_amd64.deb
$ wget https://github.com/intel/intel-graphics-compiler/releases/download/igc-1.0.11378/intel-igc-opencl_1.0.11378_amd64.deb
$ wget https://github.com/intel/compute-runtime/releases/download/22.25.23529/intel-level-zero-gpu-dbgsym_1.3.23529_amd64.ddeb
$ wget https://github.com/intel/compute-runtime/releases/download/22.25.23529/intel-level-zero-gpu_1.3.23529_amd64.deb
$ wget https://github.com/intel/compute-runtime/releases/download/22.25.23529/intel-opencl-icd-dbgsym_22.25.23529_amd64.ddeb
$ wget https://github.com/intel/compute-runtime/releases/download/22.25.23529/intel-opencl-icd_22.25.23529_amd64.deb
$ wget https://github.com/intel/compute-runtime/releases/download/22.25.23529/libigdgmm12_22.1.3_amd64.deb
```

The compute runtime will give us access to OpenCL as well as the Intel Level-Zero interface. And this is exactly what TornadoVM needs to run OpenCL and SPIR-V code on the accelerator.

Now, if we run the `clinfo` command, you should see the new version installed:

```bash
$ clinfo | grep "Driver Version"
 Driver Version                              	22.25.23529
```

Now, let’s install TornadoVM.


## Installing TornadoVM

Installing TornadoVM under Ubuntu/Fedora and CentOS is straightforward. We clone the repository and execute the auto-installer with the following options:


```bash
$ git clone https://github.com/beehive-lab/TornadoVM
$ cd TornadoVM
$ ./scripts/tornadovmInstaller.sh --jdk17 --opencl --spirv
$ . source.sh
```
 
Now TornadoVM has been installed. Let’s explore the accelerators we have available with the following command (`tornado --devices`):

<p align="center">
<img width="700" height="" src="https://raw.githubusercontent.com/jjfumero/jjfumero.github.io/master/images/blogs/wsl-tornadovm/tornadovm-wsl.png">
</p>


This command prints the backends and devices of TornadoVM. This output means that the TornadoVM runtime found two backends (one for SPIR-V and one for OpenCL). Each backend has one device available, an Intel Integrated Graphics. Note that we do not have two GPUs, it is the same GPU but accessible from different backends.
Now we can run applications and check performance:
If we run on device “0:0:” we will execute our application on the Intel HD Graphics using the SPIR-V + Level Zero backend of TornadoVM:

```bash
$ tornado -Ds0.t0.device=0:0 -m tornado.examples/uk.ac.manchester.tornado.examples.compute.MatrixMultiplication2D

Computing MxM of 512x512
    	Single Threaded CPU Execution: 3.27 GFlops, Total time = 82 ms
    	TornadoVM Execution on GPU (Accelerated): 17.90 GFlops, Total Time = 15 ms
    	Speedup: 5.46x
    	Verification true
```

We can also run on the same device using the OpenCL backend of TornadoVM (device 1:0 in my case):


```bash
$ tornado -Ds0.t0.device=1:0 -m tornado.examples/uk.ac.manchester.tornado.examples.compute.MatrixMultiplication2D

Computing MxM of 512x512
    	Single Threaded CPU Execution: 3.89 GFlops, Total time = 69 ms
    	TornadoVM Execution on GPU (Accelerated): 12.20 GFlops, Total Time = 22 ms
    	Speedup: 3.13x
    	Verification true
```

Note that, even running on the same device, the performance is different. This is expected due to the use of different software stacks (the SPIR-V backend uses the Level Zero driver for data management and orchestration of the execution, while the OpenCL backend of TornadoVM uses the OpenCL runtime). Another reason to achieve different performance is that thread-block selection is different between these two backends, and this can influence quite a lot in performance.


## Links

[1] [https://docs.microsoft.com/en-us/windows/wsl](https://docs.microsoft.com/en-us/windows/wsl)

[2] [https://docs.microsoft.com/en-us/windows/ai/directml/gpu-tensorflow-wsl#intel](https://docs.microsoft.com/en-us/windows/ai/directml/gpu-tensorflow-wsl#intel)


[3] [https://github.com/intel/compute-runtime/releases/tag/22.25.23529](https://github.com/intel/compute-runtime/releases/tag/22.25.23529)


________________________________

<a href="https://www.buymeacoffee.com/snatverk" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" style="height: 60px !important;width: 217px !important;" ></a>

