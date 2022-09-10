---
title: 'Installing CUDA, OpenCL and Level Zero in OpenSUSE Leap 15'
date: 2022-09-09
permalink: /posts/2022/09/opensuse-leap-cuda-opencl-levelzero-installation/
author_profile: false
tags:
  - OpenSUSE Leap 15
  - CUDA
  - OpenCL
  - Level Zero
  - Installation
  - Drivers
excerpt: "In this post, we show how to install the NVIDIA drivers to get access to CUDA and OpenCL parallel programming frameworks and utilities for NVIDIA GPUs. We also show how to install the Intel compute-runtime drivers for accessing, via OpenCL and Level Zero, Intel Integrated Graphics."
---

## Intro

Dealing with GPU drivers for Linux systems can sometimes be overwhelming and a tedious process. In this post, we show how to install the NVIDIA drivers to get access to CUDA and OpenCL parallel programming frameworks and utilities for NVIDIA GPUs. We also show how to install the Intel compute-runtime drivers for accessing, via OpenCL and Level Zero, Intel Integrated Graphics. We showcase the installation process for OpenSUSE LEAP. However, other Linux distributions have similar packages and installers available (e.g., for Ubuntu, CentOS, Fedora, RHEL, etc). 


## NVIDIA Drivers and CUDA 

To be able to program with CUDA, we need two major software components:  
  - The NVIDIA GPU Driver 
  - The CUDA Toolkit 


In OpenSUSE Linux, installing the NVIDIA Drivers is a relatively easy process. From the OpenSUSE documentation [https://en.opensuse.org/SDB:NVIDIA_drivers](https://en.opensuse.org/SDB:NVIDIA_drivers), we add the NVIDIA official repository: 


```bash 
$ sudo zypper addrepo --refresh 'https://download.nvidia.com/opensuse/leap/$releasever' NVIDIA 
``` 

And then you can check all NVIDIA drivers available with the following command: 

```bash 
$ zypper se x11-video-nvidiaG0* 
``` 

To install the driver, we choose a package from the list printed from the previous command. In my case, I have a NVIDIA RTX 3070, so the following command will do the installation of the latest driver: 


```bash 
$ sudo zypper in x11-video-nvidiaG06 
$ sudo zypper in nvidia-glG06 
``` 

Once the installation is finished, we restart the computer. Note that, if we have the secure boot enable, you will be prompted to install a new key. To install it, you need to enroll the new key using the root password from your OpenSUSE installation. 


### Installing CUDA 

Once the driver is installed, you can reboot your system and check with the following command: 

```bash 
$ nvidia-smi 
Fri Sep  9 09:44:18 2022 
+-----------------------------------------------------------------------------+ 
| NVIDIA-SMI 515.65.01    Driver Version: 515.65.01    CUDA Version: 11.7     | 
|-------------------------------+----------------------+----------------------+ 
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC | 
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. | 
|                               |                      |               MIG M. | 
|===============================+======================+======================| 
|   0  NVIDIA GeForce ...  Off  | 00000000:01:00.0  On |                  N/A | 
|  0%   36C    P8    10W / 220W |    234MiB /  8192MiB |      9%      Default | 
|                               |                      |                  N/A | 
+-------------------------------+----------------------+----------------------+ 
``` 

To install CUDA, we need to access the NVIDIA website and download the CUDA Toolkit installer: 

[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)


I usually install the NVIDIA GPU driver first, check that everything works and then install the CUDA Toolkit. Since the driver is already installed at this point, I download the CUDA local installation file by selecting the following tabs: 

Select Linux -> x86_84 -> OpenSUSE -> 15 -> runfile (local). 

This selection will prompt the steps to do using the latest NVIDIA CUDA Toolkit. 

At the time of writing this post: 

```bash 
$ wget https://developer.download.nvidia.com/compute/cuda/11.7.1/local_installers/cuda_11.7.1_515.65.01_linux.run 
$ sudo sh cuda_11.7.1_515.65.01_linux.run 
``` 

By running the local installer, you can select the packages you want. Since we just installed the latest driver, you can skip this package and only install the NVIDIA CUDA Toolkit: 


```bash
┌──────────────────────────────────────────────────────────────────────────────┐
│ CUDA Installer                                                               │
│ - [ ] Driver                                                                 │
│      [ ] 515.65.01                                                           │
│ + [X] CUDA Toolkit 11.7                                                      │
│   [X] CUDA Demo Suite 11.7                                                   │
│   [X] CUDA Documentation 11.7                                                │
│ - [ ] Kernel Objects                                                         │
│      [ ] nvidia-fs                                                           │
│   Options                                                                    │
│   Install                                                                    │
│                                                                              │
│ Up/Down: Move | Left/Right: Expand | 'Enter': Select | 'A': Advanced options │
└──────────────────────────────────────────────────────────────────────────────┘
```


Once the installation is finished, we can append the following env-variables to the `$HOME/.bashrc` file: 


```bash
## ###############################################
## CUDA and NVIDIA OpenCL
## ###############################################
export CPLUS_INCLUDE_PATH=/usr/local/cuda/include:$CPLUS_INCLUDE_PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:LD_LIBRARY_PATH
export PATH=/usr/local/cuda/bin/:$PATH
```

#### Check CUDA installation 

```bash
$ cd /usr/local/cuda/extras/demo_suite/
./deviceQuery 
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "NVIDIA GeForce RTX 3070"
  CUDA Driver Version / Runtime Version          11.7 / 11.7
  CUDA Capability Major/Minor version number:    8.6
  Total amount of global memory:                 7974 MBytes (8361213952 bytes)
  (46) Multiprocessors, (128) CUDA Cores/MP:     5888 CUDA Cores
  GPU Max Clock rate:                            1725 MHz (1.73 GHz)
  Memory Clock rate:                             7001 Mhz
  Memory Bus Width:                              256-bit
  L2 Cache Size:                                 4194304 bytes
  Maximum Texture Dimension Size (x,y,z)         1D=(131072), 2D=(131072, 65536), 3D=(16384, 16384, 16384)
  Maximum Layered 1D Texture Size, (num) layers  1D=(32768), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(32768, 32768), 2048 layers
  Total amount of constant memory:               65536 bytes
  Total amount of shared memory per block:       49152 bytes
  Total number of registers available per block: 65536
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  1536
  Maximum number of threads per block:           1024
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 2 copy engine(s)
  Run time limit on kernels:                     Yes
  Integrated GPU sharing Host Memory:            No
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Device supports Compute Preemption:            Yes
  Supports Cooperative Kernel Launch:            Yes
  Supports MultiDevice Co-op Kernel Launch:      Yes
  Device PCI Domain ID / Bus ID / location ID:   0 / 1 / 0
  Compute Mode:
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 11.7, CUDA Runtime Version = 11.7, NumDevs = 1, Device0 = NVIDIA GeForce RTX 3070
Result = PASS
```

To use OpenCL, we need to do a soft link for the following file:

```bash
$ cd /usr/lib64/
$ sudo ln -s libOpenCL.so.1 libOpenCL.so
```


## Install OpenCL/Level Zero for Intel architecture via the Intel Compute-Runtime 

If we have an Intel i5, i7, or i9 Intel CPU, we probably have access to the Intel Integrated GPU through the OpenCL and Level Zero APIs. To do so, we need to install the driver and the OpenCL toolkit. For OpenSUSE I recommend following the instructions from the vendor's website: 

 
[https://dgpu-docs.intel.com/installation-guides/suse/suse-15sp3.html](https://dgpu-docs.intel.com/installation-guides/suse/suse-15sp3.html)


```bash
$ sudo zypper addrepo -r https://repositories.intel.com/graphics/sles/15sp3/intel-graphics.repo
$ sudo zypper update
$ sudo zypper install \
  intel-opencl \
  intel-media-driver libmfx1 \
  intel-level-zero-gpu level-zero
## Install packages for development
$ sudo zypper install \
  libigdfcl-devel \
  intel-igc-cm \
  libigfxcmrt-devel \
  level-zero-devel
```

### Install the latest Level Zero implementation

```bash
$ mkdir -p ~/bin/
$ cd ~/bin
$ git clone https://github.com/oneapi-src/level-zero 
$ cd level-zero
$ mkdir build
$ cd build
$ cmake ..
$ cmake --build . --config Release
```

