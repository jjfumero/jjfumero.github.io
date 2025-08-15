---
title: 'How to Install NVIDIA Drivers and CUDA Toolkit on Oracle Linux 10'
date: 2025-08-15
permalink: /posts/2025/08/15/nvidia-drivers-and-toolkit-oracle-linux10
author_profile: false
tags:
 - CUDA Toolkit
 - Drivers
 - Installation
 - Linux
 - NVIDIA 
 - Oracle Linux 10
excerpt: "How to install NVIDIA 580 Drivers and CUDA 13.0 Toolkit on Oracle Linux 10"
---

For any developer or power user who's ever tried to install NVIDIA drivers on Linux, the process can feel less like a straightforward task. While most mainstream distributions have streamlined the process, trying to get NVIDIA drivers working on Oracle Linux for a desktop setup is a unique challenge. The available documentation is often sparse, focusing on server-side GPU acceleration rather than desktop graphics, leaving a trail of broken dependencies and black screens in its wake (*and that just happened to me while I was trying to install the drivers as well* 😢).

This guide aims to fill that gap. We'll walk through the process step-by-step, demystifying the installation of NVIDIA drivers on the latest Oracle Linux 10 for desktop setups. Note, for data centers, [NVIDIA guidelines](https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/) covers how to install and configure the NVIDIA driver for servers. 

In addition, this post also shows how to configure CUDA 13.0 SDK to compile and run our CUDA programs on NVIDIA GPUs. 

### 1. Update the system

First of all, we need to update the system. This installation guideline assumes a fresh installation of the Oracle Linux 10 with Gnome 47.4.

```bash
sudo dnf update
reboot
```

At the time of writing this post, this the the latest Linux Kernel available for Oracle Linux 10:

```bash
$ uname -a
Linux oraclelinux 6.12.0-101.33.4.3.el10uek.x86_64 #1 SMP PREEMPT_DYNAMIC Mon Jul 14 18:29:21 PDT 2025 x86_64 GNU/Linux
```

So, keep in mind we are using a [UEK](https://docs.oracle.com/en/operating-systems/uek/) (Unbreakable Enterprise Kernel) Linux kernel. 
This is a Linux Kernel developed by Oracle for the Oracle Linux distribution that is optimized for the Oracle Cloud. **Thus, when we install the kernel pre-requisites for the NVIDIA drivers, we need 
to install also the UEK versions.**

### 2. Download the latest NVIDIA Driver

Visit the [NVIDIA website](https://www.nvidia.com/en-us/drivers/) to download the latest NVIDIA driver. 
At the time of writing this post, the latest version is `580.76.05`.

Download the file and give execution permisions:

```bash
chmod +x NVIDIA-Linux-x86_64-580.76.05.run
```

### 3. Installing the dependencies

Enable epel Oracle Epel Repo:

```bash
sudo dnf install oracle-epel-release-el10-1.0-2.el10.x86_64
```

Install the dependencies:

```bash
sudo dnf install kernel-uek-devel gcc make acpid libglvnd-glx libglvnd-opengl libglvnd-devel pkgconfig xorg-x11-server-Xwayland libxcb egl-wayland
```

### 4. Disable Nouveau and NOVA Core

Access as root:

```bash
sudo su -
```

Then disable the `nouveau` and `nova_core` drivers:

```bash
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nova_core" >> /etc/modprobe.d/blacklist.conf
echo "options nvidia NVreg_PreserveVideoMemoryAllocations=1" >> /etc/modprobe.d/nvidia.conf
echo "options nvidia-drm modeset=1 fbdev=0" >> /etc/modprobe.d/nvidia.conf
```

### 5. Update the grub2 configuration

As described in the excellent [if-not-true-then-false](https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/) blog, we then need to update the `grub2` con configuration and create a new image. 

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
```

```bash
mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r)-nouveau-nova.img
dracut /boot/initramfs-$(uname -r).img $(uname -r)
```

### 6. Install the NVIDIA Driver

Disable the graphical interface to install the NVIDIA Driver. 
We will enable it again once the installation is done.

```bash
systemctl set-default multi-user.target
```

Then we can reboot:

```bash
reboot
```

To install the NVIDIA driver, just run the following script, and follow the instructions:

```bash
sudo ./NVIDIA-Linux-x86_64-580.76.05.run
```

Once the installation is done, enable enable the graphics interface:

```bash
systemctl set-default graphical.target
reboot
```

Check with the `nvidia-smi` command:

```bash
Fri Aug 15 08:52:12 2025
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.76.05              Driver Version: 580.76.05      CUDA Version: 13.0     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 2060 ...    Off |   00000000:01:00.0 Off |                  N/A |
| N/A   40C    P8              2W /   65W |       4MiB /   6144MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A            2744      G   /usr/bin/gnome-shell                      1MiB |
+-----------------------------------------------------------------------------------------+
```

Great, installation is done! Now, let's compile and run some CUDA programs on the GPU.
To do that, we need to install the CUDA Toolkit.

### 7. Install CUDA 13.0 Toolkit 

```bash
wget https://developer.download.nvidia.com/compute/cuda/13.0.0/local_installers/cuda-repo-rhel9-13-0-local-13.0.0_580.65.06-1.x86_64.rpm
sudo rpm -i cuda-repo-rhel9-13-0-local-13.0.0_580.65.06-1.x86_64.rpm
sudo dnf clean all
sudo dnf -y install cuda-toolkit-13-0
```

Now we can run CUDA. Update the `PATH` and the `CPLUS_INCLUDE_PATH` to include the CUDA libraries and CUDA compiler. You can add the following lines in your `~/.bashrc`:

```bash
export CPLUS_INCLUDE_PATH=/usr/local/cuda/include
export LD_LIBRARY_PATH=/usr/local/cuda/lib64
export PATH=/usr/local/cuda/bin/:$PATH
```

And done!

Let's try a few examples:

### 8. Download CUDA Sample Suite


```bash
git clone https://github.com/NVIDIA/cuda-samples
cd cuda-samples
cd Samples/0_Introduction/matrixMul/
mkdir build
cd build
cmake ..
make
```

Now we can run the example:

```bash
./matrixMul
[Matrix Multiply Using CUDA] - Starting...
GPU Device 0: "Turing" with compute capability 7.5

MatrixA(320,320), MatrixB(640,320)
Computing result using CUDA Kernel...
done
Performance= 382.48 GFlop/s, Time= 0.343 msec, Size= 131072000 Ops, WorkgroupSize= 1024 threads/block
Checking computed result for correctness: Result = PASS

NOTE: The CUDA Samples are not meant for performance measurements. Results may vary when GPU Boost is enabled.
```

### References

[1] [https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/](https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/)

[2] [https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/](https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/
)