---
title: 'Configuration of the NVIDIA and Intel GPU drivers for RHEL9'
date: 2023-05-19
permalink: /posts/2023/05/rhel-gpu-drivers
author_profile: false
tags:
  - RHEL
  - GPU Drivers
  - NVIDIA CUDA
  - Intel HD Graphics
  - Intel OpenCL 
  - Intel Level Zero
excerpt: "This post shows the installation steps to obtain NVIDIA CUDA and Intel OpenCL and Level Zero runtimes to run applications on GPUs with RHEL."
---

## Introduction

I have been using Fedora for many years, but the end-of-life release cycles (usually every 13 months) are one of the things that are hard to keep up with. Fedora recommends staying updated with the Fedora release schedule and planning upgrades to newer versions within the Maintenance Support period to ensure continued support, security, and access to new features. 

However, although Fedora provides utilities and documentation to migrate from one version to another, I haven't had a good experience when migrating also with the GPU drivers (e.g., NVIDIA GPU and Intel GPU drivers). For my needs, at least, I need more stable and long-support OS versions. 

In the past, I have also used CentOS, mainly for servers, which I really liked. GPU driver packages are up to date and migration to new versions usually happens very smoothly. This time I wanted to give it a try to RHEL and its software ecosystem. Given that RHEL is free for developers [1], I took the opportunity to configure an old laptop with this OS and the latest GPU drivers for NVIDIA GPU and Intel integrated graphics. 

This post shows the installation steps to obtain NVIDIA CUDA and Intel OpenCL and Level Zero runtimes to run applications on GPUs. Although there is extensive documentation for the installation of the NVIDIA drivers, I found it a bit tricky. Furthermore, there is no information about how to install the Intel drivers for the Intel-integrated GPUs from its GitHub repository [3]. Thus, this post tries to clarify the installation process for RHEL. 


### NVIDIA Drivers and CUDA 

The installation of the NVIDIA Drivers is straightforward. Although I will list the commands in this post, I always recommend following the latest guidelines [2] from the vendor’s website. In my case, I tested the following commands with RHEL 9.2 to install the NVIDIA 530.30.02 driver and CUDA 12.1. 

#### 1. Install the dependencies 

```bash
sudo yum group install "Development Tools"
sudo dnf install kernel-devel-$(uname -r) kernel-headers-$(uname -r)
```

#### 2. Configure the NVIDIA packages:

```bash
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
subscription-manager repos --enable=rhel-9-for-x86_64-appstream-rpms
subscription-manager repos --enable=rhel-9-for-x86_64-baseos-rpms
subscription-manager repos --enable=codeready-builder-for-rhel-9-x86_64-rpms
sudo rpm --erase gpg-pubkey-7fa2af80*
```


#### 3. Install the NVIDIA driver and CUDA


```bash
sudo dnf config-manager --add-repo https://developer.download.nvidia.com/compute/cuda/repos/rhel9/x86_64/cuda-rhel9.repo
sudo dnf clean expire-cache
sudo dnf module install nvidia-driver:latest-dkms
sudo dnf install cuda
sudo dnf install nvidia-gds
```

Reboot the machine and execute the nvidia-smi:


```bash
$ nvidia-smi 
Fri May 19 09:34:22 2023       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 530.30.02              Driver Version: 530.30.02    CUDA Version: 12.1     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                  Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf            Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce GTX 1050         Off| 00000000:01:00.0 Off |                  N/A |
| N/A   52C    P3               N/A /  N/A|   1207MiB /  4096MiB |     14%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      2359      G   /usr/libexec/Xorg                           407MiB |
|    0   N/A  N/A      2528      G   /usr/bin/gnome-shell                        214MiB |
|    0   N/A  N/A      3359      G   ...8934324,13005600369256201419,262144      181MiB |
|    0   N/A  N/A      4307      G   ...6695823,10865757492502058533,262144      402MiB |
+---------------------------------------------------------------------------------------+
```


#### 4. Export the env variables for CUDA:

```bash
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
export PATH=/usr/local/cuda/bin:$PATH
export CPLUS_INCLUDE_PATH=/usr/local/cuda/include:$CPLUS_INCLUDE_PATH
export C_INCLUDE_PATH=/usr/local/cuda/include:$C_INCLUDE_PATH
```

#### 5. Fix the link for OpenCL:

I am not sure why, the OpenCL configuration misses a soft link. We can fix it as follows:


```bash
cd /usr/lib64/
sudo ln -s libOpenCL.so.1 libOpenCL.so
```

Now we can run CUDA and OpenCL applications on the NVIDIA GPU. 


## Installation of OpenCL and Level Zero Drivers for Intel HD Graphics

To install the OpenCL and Level Zero drivers for the Intel integrated graphics, what we need is the compute-runtime driver [3]. However, the installation packages are only available for Ubuntu 22.04 LTS. I remember to obtain also these packages for Fedora from this GitHub. but, for some reason, these packages are not available any more. 

So, what I did was to search for the information to install the Intel Graphics (for the intel brand new discrete GPUs) and obtained the instructions related to the runtime [4]. To be honest, I did not even know this will work. Since I do not have an ARC (discrete GPU), I only need the packages related to the runtime, so I took a guess and, it worked! 

```bash
$ sudo dnf config-manager   --add-repo   https://repositories.intel.com/graphics/rhel/9.2/flex/intel-graphics.repo
$ sudo dnf config-manager   --add-repo   https://repositories.intel.com/graphics/rhel/8.6/flex/intel-graphics.repo
$ sudo dnf clean all
$ sudo dnf makecache       
$ sudo dnf install cmake
$ sudo dnf install make
$ sudo dnf install intel-opencl intel-media intel-mediasdk libmfxgen1 libvpl2  level-zero intel-level-zero-gpu mesa-dri-drivers mesa-vulkan-drivers   mesa-vdpau-drivers libdrm mesa-libEGL mesa-libgbm mesa-libGL   mesa-libxatracker  libvpl-tools intel-metrics-discovery   intel-metrics-library intel-igc-core intel-igc-cm   libva libva-utils  intel-gmmlib intel-ocloc 
```

Development packages:

```bash
$ sudo dnf install --refresh   intel-igc-opencl-devel   level-zero-devel   intel-gsc-devel   libmetee-devel   level-zero-devel
```

#### [Optional] Install clinfo

```bash
sudo dnf install clinfo
clinfo  | grep "Device Name"
  Device Name       NVIDIA GeForce GTX 1050
  Device Name       Intel(R) HD Graphics 630
```


## Links

[1] [https://developers.redhat.com/products/rhel/download](https://developers.redhat.com/products/rhel/download)

[2] [https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#rhel-9-rocky-9](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#rhel-9-rocky-9)

[3] [https://github.com/intel/compute-runtime](https://github.com/intel/compute-runtime)

[4] [https://dgpu-docs.intel.com/installation-guides/redhat/redhat-8.6.html](https://dgpu-docs.intel.com/installation-guides/redhat/redhat-8.6.html)

