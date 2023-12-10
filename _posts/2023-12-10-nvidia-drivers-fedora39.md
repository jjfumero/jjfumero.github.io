---
title: 'Installing the NVIDIA Drivers and CUDA 12.3 on Fedora 39 with Secure Boot Enabled'
date: 2023-12-10
permalink: /posts/2023/12/nvidia-cuda-drivers-fedora39
author_profile: false
tags:
  - NVIDIA
  - CUDA
  - Installation
  - Drivers
  - Fedora39 
excerpt: "Installing the NVIDIA Drivers and CUDA 12.3 on Fedora 39 with Secure Boot Enabled."
---

Some time ago, [I wrote some guidelines](https://snatverk.blogspot.com/2022/04/installing-nvidia-driver-and-cuda-on.html) about how to install and configure the NVIDIA and CUDA drivers on Fedora 36 when the secure boot is enabled. This post revisits this installation process with a fresh-installed Fedora 39 workstation. It also explains how to get dual monitor setup using NVIDIA Prime to display from both an NVIDIA Discrete GPU and an Intel integrated graphics, and how to get CUDA programs running.


## 1. Installing a signed driver with Secure Boot

The easiest way to install the NVIDIA drivers in Fedora Linux is via the [RPM-Fusion packages](https://phoenixnap.com/kb/fedora-nvidia-drivers). 
However, as far as I know, and based on my experience, there is no way to install it from the repositories when the secure boot is enabled. 
Thus, to install the NVIDIA driver, we need to perform a manual installation using the script provided by NVIDIA. 

The website [if-not-true-then-false](https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide) provides a step-by-step guideline for manually installing the NVIDIA drivers on Fedora Linux for a wide set of versions (from Fedora 37 to Fedora 39 at the time of writing this post). 
However, when we perform the manual installation, we find the following error:


```bash
ERROR: Unable to load the `nvidia-drm` kernel module
```a

This is due to the secure boot being enabled in the BIOS. 
However, if we must preserve the secure boot enabled from the BIOS, we need to sign the driver. Note that, if other OSs are installed, e.g., Windows, we may lose those installations when the secure boot is disabled. In order to install the NVIDIA driver and preserve the secure boot, we can follow the guidelines explained [in this link](https://askubuntu.com/questions/1023036/how-to-install-nvidia-driver-with-secure-boot-enabled). 
To sum up, we sign the driver using the following commands:

```bash
$ cd $home 
$ mkdir -p bin/drivers 
$ openssl req -new -x509 -newkey rsa:2048 -keyout /home/$USER/bin/drivers/Nvidia.key -outform DER -out /home/$USER/bin/drivers/Nvidia.der -nodes -days 100000 -subj "/CN=Graphics Drivers"
$ sudo mokutil --import /home/$USER/bin/drivers/Nvidia.der
```

As mentioned in [link1](https://askubuntu.com/questions/1023036/how-to-install-nvidia-driver-with-secure-boot-enabled) by [itpropmn07](https://askubuntu.com/users/843562/itpropmn07):

 > “This command requires you create password for enrolling. Afterwards, reboot your computer, in the next boot, the system will ask you enroll, you enter your password (which you created in this step) to enroll it. Read more: https://sourceware.org/systemtap/wiki/SecureBoot”

## 2. Installing the NVIDIA Driver

The website [if-not-true-then-false](https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide) is an excellent resource to install the NVIDIA drivers for different Operating Systems. 
In a nutshell, we perform the following commands:

```bash
## Install dependencies
dnf install kernel-devel kernel-headers gcc make dkms acpid libglvnd-glx libglvnd-opengl libglvnd-devel pkgconfig

## blacklist nouveau
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf

## edit /etc/default/grub
GRUB_CMDLINE_LINUX="rhgb quiet rd.driver.blacklist=nouveau nvidia-drm.modeset=1"

## Update grub2
grub2-mkconfig -o /boot/grub2/grub.cfg

## remove old package
dnf remove xorg-x11-drv-nouveau

## Backup old initramfs nouveau image 
mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r)-nouveau.img
## Create new initramfs image ##
dracut /boot/initramfs-$(uname -r).img $(uname -r)
systemctl set-default multi-user.target

```

After a reboot in multi-user.target mode (mode 3), we can install the NVIDIA driver: 

```bash
$ sudo ./NVIDIA-Linux-x86_64-535.146.02.run
--module-signing-secret-key=/home/$USER/bin/drivers/Nvidia.key 
--module-signing-public-key=/home/$USER/bin/drivers/Nvidia.der
```

Once the installer script finishes, we reboot again with the `graphical.target` mode enabled.

```bash
$ systemctl set-default graphical.target
$ reboot
$ nvidia-smi 

Sun Dec 10 15:49:57 2023       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.146.02             Driver Version: 535.146.02   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 3070        Off | 00000000:01:00.0  On |                  N/A |
|  0%   41C    P8              14W / 220W |    642MiB /  8192MiB |     28%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      2193      G   /usr/libexec/Xorg                           203MiB |
|    0   N/A  N/A      2418      G   /usr/bin/gnome-shell                        187MiB |
|    0   N/A  N/A      3351      G   /usr/bin/nautilus                             9MiB |
|    0   N/A  N/A      3477      G   /usr/lib64/firefox/firefox                  135MiB |
|    0   N/A  N/A      4479      G   ...sion,SpareRendererForSitePerProcess       92MiB |
+---------------------------------------------------------------------------------------+ 
```


## 3. Installing CUDA


The NVIDIA website contains all the instructions for all supported operating systems. 
In the case of Fedora 39, we can apply the following commands:


```bash
wget https://developer.download.nvidia.com/compute/cuda/12.3.1/local_installers/cuda-repo-fedora37-12-3-local-12.3.1_545.23.08-1.x86_64.rpm
sudo rpm -i cuda-repo-fedora37-12-3-local-12.3.1_545.23.08-1.x86_64.rpm
sudo dnf clean all
sudo dnf -y install cuda-toolkit-12-3
```

Note that we skipped the driver installation because it was manually installed in the first step.


## 4. Configuring Displays to render from iGPU and dGPU. 

The installation and configuration process that we have just made allow us to display and run commands using the NVIDIA GPU. But what if have dual monitor system, each displaying from a different GPU? 
One might ask, why do you need this? Well, for unknown reasons, Dell updated the BIOS and I can't see the integrated GPU, unless there is a cable and a screen connected to it. 
So, if I want both GPUs to run, let’s say, OpenCL, or oneAPI, I need at least one screen connected to the HDMI/DisplayPort of the Integrated GPU. 

So, how can we achieve this? We need to enable [NVIDIA Prime Synchronization](https://forums.developer.nvidia.com/t/prime-and-prime-synchronization/44423). 
As state in the NVIDIA Forums: 

 > The goal of PRIME is to allow the NVIDIA GPU to share its output with the integrated GPU so that it can be presented to the display.


Following [the official documentation](http://us.download.nvidia.com/XFree86/Linux-x86_64/370.23/README/randr14.html), we apply the following change to the `/etc/X11/xorg.org`:

```bash
Section "Module"
    Load "modesetting"
EndSection

Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BusID          "PCI:1:0:0"
    Option 	   "AllowEmptyInitialConfiguration"
EndSection
```

Then you can reboot. In my case, this enabled the two screens, and I can see both GPUs visible on my systems (the RTX 3070 and the Intel HD Graphics).


## 5. Configuring GCC 12 for CUDA 

Fedora 39 comes with GCC 13.2.1 by default. However, if you want to run CUDA, you might get the following error:

```bash
In file included from /usr/local/cuda/bin/../targets/x86_64-linux/include/cuda_runtime.h:82,
                 from <command-line>:
/usr/local/cuda/bin/../targets/x86_64-linux/include/crt/host_config.h:143:2: error: #error -- unsupported GNU version! gcc versions later than 12 are not supported! The nvcc flag '-allow-unsupported-compiler' can be used to override this version check; however, using an unsupported host compiler may cause compilation failure or incorrect run time execution. Use at your own risk.
  143 | #error -- unsupported GNU version! gcc versions later than 12 are not supported! The nvcc flag '-allow-unsupported-compiler' can be used to override this version check; however, using an unsupported host compiler may cause compilation failure or incorrect run time execution. Use at your own risk.
```

Thus, we need to configure GCC 12. 
There are many package managers to do this. 
One of them is [spack](https://github.com/spack/spack).

As suggested [here](https://jchuynh.medium.com/how-to-solve-cuda-incompatibility-with-high-versions-of-gcc-f47ef966bb15):


```bash
$ sudo dnf install patch 
```

And then: 

```bash
$ git clone https://github.com/spack/spack.git
$ source spack/share/spack/setup-env.sh
$ spack install gcc@12.3.0 
$ slack load gcc@12.3.0
```

Happy coding! 


